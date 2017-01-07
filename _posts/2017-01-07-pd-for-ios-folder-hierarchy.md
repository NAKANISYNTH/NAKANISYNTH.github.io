---
layout: post
title:  "pd-for-iosを使うにあたってつまづいたこと"
categories: iOS
---

[iOSアプリでPureDataをサウンドエンジンとして利用する](/ios/2017/01/01/introduction-libpd-swift.html)というメモを書いた後に機能を追加していく上で色々つまづいてしまったのでメモ。
テストするためにボタンをタップするとPdいろいろ送るプログラムを書きました。
コードは**[こちら](https://github.com/NAKANISYNTH/Swift-libpd-example/tree/withSubpatches)**

# Pdが正しくlistを受け取ってくれない

原因はlibpd経由でlistを送った場合は最初に"list"という文字列が入ってくる仕様にあった。なのでレシーブオブジェクトの直下に[route list]を繋いでやる必要がある。
ほかの`sendBangToReceiver`とか`sendFloat`は値がそのまま送られるのでrouteで振り分ける必要はない。

# サブパッチにしたところの処理が走らない

最初書いてたファイルを開く処理がこちら
{% highlight swift %}
PdBase.openFile("pd-patch/main.pd" path:Bundle.main.resourcePath)
{% endhighlight %}
のようにファイル名の引数にパスを渡すとmain.pdと同じ階層にあるサブパッチでも認識されないようで全く動作しなくなる。
正しくは
{% highlight swift %}
PdBase.openFile("main.pd", path: Bundle.main.resourcePath!+"/pd-patches")
{% endhighlight %}