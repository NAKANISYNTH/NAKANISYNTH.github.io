---
layout: post
title:  "iOSアプリでPureDataをサウンドエンジンとして利用する"
categories: iOS
---

PureDataのライブラリlibpdのiOS版pd-for-iosを導入する方法をまとめています。
環境はXcode8.2.2, Swift2.3。導入する対象のプロジェクトはすでにgitで管理されている前提です。

# pd-for-iosをsubmoduleとして追加する
terminalからプロジェクトのリポジトリ内で

```
$git clone https://github.com/libpd/pd-for-ios
$cd pd-for-ios/
pd-for-ios $git submodule init
pd-for-ios $git submodule update
pd-for-ios $cd ..
$git submodule add pd-for-ios/
```
pd-for-iosとそのサブモジュールpure-dataがそれぞれmasterブランチになっていることを確認する。
私の場合クローン直後はHEADになってて、必要なファイルがなくてビルドが通らなかった。

# Bridging-Headerを作成
pd-for-iosはObjective-Cで書かれているので、Swiftのプロジェクトで読み込むにはBridging Headerが必要。
new fileからヘッダーを選んでファイル作成。名前を"<プロジェクト名>-Bridging-Header.h"に変更してbuild settingsのswift compiler - GeneralのObjective-C Bridging Headerの項目にパス"$(PROJECT_DIR)/$(PROJECT_NAME)/$(PROJECT_NAME)-Bridging-Header.h"を記述。


# libpdをプロジェクトに組み込む
libpd.xcodeprojファイルをプロジェクトナビゲーターにドラッグ・アンド・ドロップ
GeneralからLinked Frameworks and Librariesのlibpd-ios.aを追加。
user header search pathにpd-for-ios/libpdのパスを追加。recursiveを選択すること。
![header search path]({{site.baseurl}}/assets/img/2017-01-01-headersearchpath.png)

# pdのファイルを配置
とりあえずサイン波を鳴らすだけのパッチを用意して音が鳴ることを確認する。
![add files]({{site.baseurl}}/assets/img/2017-01-02-pdpatch.png)
適当なフォルダをプロジェクトファイルと同じ階層に作成してそれをプロジェクトナビゲーターにドラッグ・アンド・ドロップ。Added foldersのCreate folder referencesをチェック。
![add files]({{site.baseurl}}/assets/img/2017-01-01-addfiles.png)

# プログラムからpdのファイルを開く

Player.swift

{% highlight swift %}
import Foundation

class Player : NSObject, PdReceiverDelegate{
    
    let audioController = PdAudioController()
    static let sharedInstance = Player()
    var pdPointer:UnsafeMutablePointer<Void>?
    
    override init() {
        super.init()
        
    }
    
    func openPdFile(){
        audioController.configurePlaybackWithSampleRate(44100, numberChannels: 2, inputEnabled: false, mixingEnabled: true)
        audioController.active = true
        
        if pdPointer == nil {
            
            pdPointer = PdBase.openFile("pd-patches/main.pd", path: NSBundle.mainBundle().resourcePath)
            print("open pd file")
        }
    }
    
    func closePdFile(){
        if pdPointer != nil{
            PdBase.closeFile(pdPointer!)
            pdPointer = nil
            print("close pd file")
        }
        audioController.active = false
    }
    
}
{% endhighlight %}

ViewController.swift

{% highlight swift %}
import UIKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        Player.sharedInstance.openPdFile()
    }

}
{% endhighlight %}

これでビルドしたらアプリを開いたタイミングで音が鳴るはず・・！



