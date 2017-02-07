---
layout: post
title:  "macOSでCoreBluetoothを使う際に注意しないといけないこと(Swift 3)"
categories: macOS
---

iOSと仕様が若干異なるので注意が必要。

## CBCentralManagerにisScanningが無い
何故か無い。無いので自分で変数を用意して管理するしかない。

## CBCentralManagerのdelegateの関数が呼ばれない
何故か初期化の際にdelegateを渡す必要があるみたい。
以下のようにするとdelegateの関数が呼ばれず動かない。

{% highlight swift %}
var centralManager = CBCentralManager()

override init() {
    super.init()
    centralManager.delegate = self //ここで渡しても呼ばれない
}

{% endhighlight %}

以下のようにイニシャライザでselfを渡すとちゃんと呼ばれる。

{% highlight swift %}
var centralManager:CBCentralManager!
override init() {
    super.init()
    centralManager = CBCentralManager(delegate: self, queue: nil)
}
{% endhighlight %}


## didReadRSSIの関数が違う
以下のようにOSで処理を分けたりしないといけない。

{% highlight swift %}
#if os(OSX)
open func peripheralDidUpdateRSSI(_ peripheral: CBPeripheral, error: Error?) {
    if let orp = availableOrphe(peripheral: peripheral){
        orp.RSSI = peripheral.rssi!
        delegate?.didUpdateRSSI?(orphe: orp)
    }
}
#else
public func peripheral(_ peripheral: CBPeripheral, didReadRSSI RSSI: NSNumber, error: Error?){
    if let orp = availableOrphe(peripheral: peripheral){
        orp.RSSI = RSSI
        delegate?.didUpdateRSSI?(orphe: orp)
        NotificationCenter.default.post(name: .OrpheDidUpdateRSSINotification, object: nil, userInfo: [OrpheDataUserInfoKey:orp])
    }
}
#endif

{% endhighlight %}
