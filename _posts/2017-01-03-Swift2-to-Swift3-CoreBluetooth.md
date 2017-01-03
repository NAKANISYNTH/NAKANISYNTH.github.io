---
layout: post
title:  "Bluetooth周りをSwift2からSwift3に移行"
categories: iOS
---

Swift2からSwift3に移行する際にビルドは通るのにdidDiscoverPeripheralとかdidConnectPeripheralが呼ばれなくてちょっと焦ったのでメモ。

# 原因
デリゲートのメソッド名が変わっただけでした

{% highlight swift %}
//old
optional public func centralManager(_ central: CBCentralManager, didDiscoverPeripheral peripheral: CBPeripheral, advertisementData: [String : AnyObject], RSSI: NSNumber)
//new
optional public func centralManager(_ central: CBCentralManager, didDiscover peripheral: CBPeripheral, advertisementData: [String : Any], rssi RSSI: NSNumber)

//old
optional public func centralManager(central: CBCentralManager, didConnectPeripheral peripheral: CBPeripheral)
//new
optional public func centralManager(_ central: CBCentralManager, didConnect peripheral: CBPeripheral)

{% endhighlight %}


単純なことなんだけど、デリゲートのメソッドは名前が変わってもエラーになってくれないからこういう時とても分かりづらいっす