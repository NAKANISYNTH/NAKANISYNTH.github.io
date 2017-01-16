---
layout: post
title:  "Swift環境にGoogle AnalyticsをCocoaPodsで導入する"
categories: iOS
---

環境はXcode8.2.1, Swift3.0です。

## CocoaPodsで必要なライブラリをインストール

インストールとかのやり方は[こちらのページ](https://developers.google.com/analytics/devguides/collection/ios/v3/?hl=ja)に丁寧に書いてあります。

このメモには簡単な手順を記載しています。

既にCocoaPodsでライブラリを入れたりしてるプロジェクトを前提にしてます。

ターゲットに  "pod 'Google/Analytics'"を追加してpod update

## 設定ファイルを取得

[ここ](https://developers.google.com/mobile/add?platform=ios&cntapi=analytics&cnturl=https%3A%2F%2Fdevelopers.google.com%2Fanalytics%2Fdevguides%2Fcollection%2Fios%2Fv3%2Fapp%3Fconfigured%3Dtrue%23add-config&cntlbl=Continue+Adding+Analytics&hl=ja)で必要事項を記入し、設定ファイルを取得する。

取得したGoogleService-Info.plistファイルをプロジェクトにドラッグ・アンド・ドロップ。コピーする。

## Bridging-Headerを作成
[iOSアプリでPureDataをサウンドエンジンとして利用する](/ios/2017/01/01/introduction-libpd-swift.html)の「Bridging-Headerを作成」に書いてます。
Google AnalyticsのライブラリはObjective-Cで書かれているので。


## 設定ファイルの読み込み

AppDelegateでやってます。

{% highlight swift %}

func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    
    var configureError: NSError?
    GGLContext.sharedInstance().configureWithError(&configureError)
    assert(configureError == nil, "Error configuring Google services: \(configureError)")
    
    return true
}

{% endhighlight %}


## トラッキングする関数を作る
UIViewControllerにextensionを追加するとトラックしたいViewControllerで関数を呼ぶだけで監視できて便利。
viewWillAppear辺りで呼べばOK。

{% highlight swift %}
extension UIViewController{
    func trackScreenView() {
        let tracker = GAI.sharedInstance().defaultTracker
        tracker?.set(kGAIScreenName, value: NSStringFromClass(type(of: self)))
        let builder: NSObject = GAIDictionaryBuilder.createScreenView().build()
        tracker?.send(builder as! [NSObject : AnyObject])
    }
}
{% endhighlight %}

これだけで完了。
あとは[Google Analyticsの管理画面](https://analytics.google.com)で監視できます。


