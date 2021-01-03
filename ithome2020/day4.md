這一篇的教學要教大家怎麼在你的場景上放置一些Image(圖片)跟對圖片做一些變化，所以會介紹到KorGE提供在[Day01](https://yayachang.github.io/ithome2020/day1)第一篇一開始就使用過的Image類別。

### 讀取Image圖片
```
image(resourcesVfs["mylogo.png"].readBitmap())
```
其實非常簡單，只要寫下上面那一行，KorGE就會到專案資料夾的resouces/mylogo.png讀取檔案圖片，然後幫你把它加入到場景上。

![https://yayachang.github.io/ithome2020/images/day04-01.png](https://yayachang.github.io/ithome2020/images/day04-01.png)

如果你好奇為什麼只要宣告這一行就能幫我顯示到場景上，你可以去trace Image.kt的程式，在inline fun Container.image就能看見最後其實他已經幫你在建立image物件時，```Image(texture, anchorX, anchorY).addTo(this, callback)```已經預設幫你加到他的Parent了，所以我們不用特別再做addChild()來添加View到場景這個動作，。

### Image.kt
```
inline fun Container.image(
   texture: Bitmap, anchorX: Double = 0.0, anchorY: Double = 0.0, callback: @ViewDslMarker Image.() -> Unit = {}
): Image = Image(texture, anchorX, anchorY).addTo(this, callback)
```

### 調整Image圖片大小
因為我放的mylogo.png的實際大小是400x400，在沒有進行縮放前，放在500x500的視窗上，當然就是右下邊都是黑色的背景，如果是像是SPLASH進版畫面，希望都是能全螢幕顯示，所以就來進行image的大小調整。
```
image(resourcesVfs["mylogo.png"].readBitmap()) {
    width = ConfigModule.size.width.toDouble()
    height = ConfigModule.size.height.toDouble()
}
```
執行結果看到圖片把黑色部份都填滿囉。

![https://yayachang.github.io/ithome2020/images/day04-02.png](https://yayachang.github.io/ithome2020/images/day04-02.png)

### 調整Image位置
在遊戲中的背景或是物品都有可能會進行移動，而且每張圖片的起始座標不會一直都從原點```(x,y) = (0,0)```，所以我們來練習讓圖片的座標放在不同的位置上。
```
val image = image(resourcesVfs["mylogo.png"].readBitmap()) {
    width = ConfigModule.size.width.toDouble()
    height = ConfigModule.size.height.toDouble()
    position(100,100)
}
```
執行結果可以看到mylogo.png確實往右下移了(100,100)，當然圖片就會超出畫面外了。

![https://yayachang.github.io/ithome2020/images/day04-03.png](https://yayachang.github.io/ithome2020/images/day04-03.png)

### 調整Image透明度
玩遊戲的經驗中，應該都有經驗當角色受到傷害時，會感覺角色本身會有一閃一閃的感覺，來表現被碰到傷害，這時可以用調整透明度這種簡單的特效來達成。或是想要表現晚上的星空的星星在閃爍，我們也能透過調整星星的透明度來做到，所以我們先來試著調整mylogo.png的透明度來練習，為在之後的特效教學做準備。
```
image(resourcesVfs["mylogo.png"].readBitmap()) {
    width = ConfigModule.size.width.toDouble()
    height = ConfigModule.size.height.toDouble()
    alpha = 0.5
}
```
執行後，可以比對預設是alpha = 1.0跟 alpha = 0.5的差異，這就是調整alpha的效果了。
![https://yayachang.github.io/ithome2020/images/day04-04.png](https://yayachang.github.io/ithome2020/images/day04-04.png)

### 旋轉Image
將圖片旋轉的動作也是遊戲中常用的行為，像是可能想要表現一顆球的滾動，除了位置的移動，物體本身做旋轉也是必須的，所以就來練習將圖片進行旋轉。
```
image(resourcesVfs["mylogo.png"].readBitmap()) {
    width = ConfigModule.size.width.toDouble()
    height = ConfigModule.size.height.toDouble()
    rotationDegrees = 45.0
}
```
執行後，有發現好像圖片真的有向右旋轉了45度，但是怎麼覺得有點怪怪的！
![https://yayachang.github.io/ithome2020/images/day04-05.png](https://yayachang.github.io/ithome2020/images/day04-05.png)

沒錯！就是有點怪怪的，因為我們預期心裡應該是照著圖片的中心點來進行向右的45度旋轉啊，為什麼會這樣呢？

原因是image物件預設的anchor point(定錨點)的座標是(0,0)，圖片就會以(0,0)為旋轉中心點去向右轉45度。所以我們只要將anchor point重新設定，在程式以加上anchor(0.5, 0.5)，表示圖片的定錨點在圖片中心了。
```
image(resourcesVfs["mylogo.png"].readBitmap()) {
    anchor(.5, .5)
    width = ConfigModule.size.width.toDouble()
    height = ConfigModule.size.height.toDouble()
    rotationDegrees = 45.0
}
```
執行後，痾痾痾…怎麼還是怪怪的啊！！沒有在中心點旋轉啊！！

![https://yayachang.github.io/ithome2020/images/day04-06.png](https://yayachang.github.io/ithome2020/images/day04-06.png)

這裡就要解釋一下，因為你的anchor point(定錨點)跑到圖片的中心，在設定image座標時，就會以定錨點的位置去移動，也就是你現在看到的結果就是圖片中心放在場景座標(0.0)的位置，然後旋轉了45度。而一開始沒感覺定錨點的存在，是剛好定錨點預設是(0,0)，圖片預設的座標也是(0,0)，因此圖片看起來就是正常的呈現，但一旋轉就變這樣了。

### 那該怎麼調整到看起來正常?
當我們設定定錨點在圖片中心anchor(0.5, 0.5)時，假設還沒旋轉的樣子，圖片會是呈現這樣。圖片中心點跑去場景的原點座標(0,0)了

![https://yayachang.github.io/ithome2020/images/day04-07.png](https://yayachang.github.io/ithome2020/images/day04-07.png)

只要把圖片的座標移到場景的中心點，也就是算出長寬一半width/2跟height/2的位置，圖片就會又會正常地呈現了。
```
image(resourcesVfs["mylogo.png"].readBitmap()) {
    anchor(0.5, 0.5)
    width = ConfigModule.size.width.toDouble()
    height = ConfigModule.size.height.toDouble()
    position(scaledWidth/2, scaledHeight/2)
}
```

*特別注意: 這裡我們是拿scaledWidth跟scaledHeight去算位置，而不是直接拿已經分配好的width跟height，原因是如果你去trace Image.kt的程式碼，發現set並不會去真的去改變image原先的圖片的長寬，而是去改變scaleX跟scaleY的數值，所以我們計算位置必須是要拿scale過的長寬唷。*

![https://yayachang.github.io/ithome2020/images/day04-08.png](https://yayachang.github.io/ithome2020/images/day04-08.png)

最後，在進行向右45度旋轉，就能達到我們想要的旋轉效果了!!
```
image(resourcesVfs["mylogo.png"].readBitmap()) {
    anchor(0.5, 0.5)
    width = ConfigModule.size.width.toDouble()
    height = ConfigModule.size.height.toDouble()
    position(scaledWidth/2, scaledHeight/2)
    rotationDegrees = 45.0
}
```

![https://yayachang.github.io/ithome2020/images/day04-09.png](https://yayachang.github.io/ithome2020/images/day04-09.png)

### 總結
這篇主要講解一些圖片的基本應用，幾乎都會應用在遊戲上，透過這些簡單的練習，在後面的部分就能舉一反三來的拿來利用了！

### 參考資料
* [https://korlibs.soywiz.com/korge/reference/views_standard/#image](https://korlibs.soywiz.com/korge/reference/views_standard/#image)
* [https://www.youtube.com/watch?v=nR_cCs_8wF8](https://www.youtube.com/watch?v=nR_cCs_8wF8)
