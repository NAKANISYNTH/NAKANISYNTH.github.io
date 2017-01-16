---
layout: post
title:  "tableViewの背景に画像を表示する"
categories: iOS
---

参考サイト:[Transparent Table View With a Background Image](https://grokswift.com/transparent-table-view/)

参考のサイトでは表示する画像の位置や大きさが自由に変更しづらかったので
tableViewのbackgroundViewにUIViewを設定してそのviewにaddSubviewで画像を貼り付けることでやりたいことが出来た。

{% highlight swift %}
override func viewDidLayoutSubviews() {
        
        // make UIImageView instance
        let imgW:CGFloat = 200
        let imgH:CGFloat = imgW
        let imgX = (self.tableView.frame.width-imgW)/2
        let imgY = (self.tableView.frame.height-imgH)/2
        let imageView = UIImageView(frame: CGRect(x: imgX, y: imgY, width: imgW, height: imgH))
        // read image
        let image = UIImage(named: "imgae_name")
        // set image to ImageView
        imageView.image = image
        imageView.alpha = 1
        imageView.contentMode = .scaleAspectFit
        self.tableView.backgroundView = UIView(frame: CGRect(x: 0, y: 0, width: self.tableView.frame.width, height: self.tableView.frame.height))
        self.tableView.backgroundView!.addSubview(imageView)
    }
    
{% endhighlight %}
