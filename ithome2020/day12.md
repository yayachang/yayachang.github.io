# [Day12] 畫面解析度-Resolution

這回要來寫KorGE的怎麼處理畫面的解析度。因為我們先前的範例都是用預設的512x512大小來顯示，但是1：1通常不會是遊戲常見的遊戲比例，至少現在都要用個16:9好像才比較符合現在常用的顯示比例。

### 實際螢幕與虛擬螢幕
KorGE在處理畫面就有分為Native Screen跟Virtual Screen，官方文件提供的螢幕解析度說明應該就表示很清楚，這個用KorGE建立的視窗，實際螢幕大小就是1920x1080，而虛擬螢幕大小就是640x480。

![https://yayachang.github.io/ithome2020/images/day11-01.png](https://yayachang.github.io/ithome2020/images/day11-01.png)

圖片資料來源: https://korlibs.soywiz.com/korge/reference/resolutions/

### 螢幕解析度設定
DSL寫法設定，實際螢幕大小1920x1080，遊戲虛擬螢幕大小640x480
```
suspend fun main() = Korge(width = 1920, height = 1080, virtualWidth = 640, virtualHeight = 480) {
}
```
也可以像Day03我們有練習過用Korge.Config傳入一個Module來設定這些視窗的屬性。
```
suspend fun main() = Korge(Korge.Config(module = ConfigModule)) {
}
object ConfigModule : Module() {
	override val size = SizeInt(640, 480)
	override val windowSize = SizeInt(1920, 1080)
}
```
寫完以上設定後，你會發現虛擬螢幕(也就是遊戲畫面)都會置中去填滿實際螢幕，然後不管你怎麼手動調整視窗的大小，虛擬畫面都會依實際螢幕比例來縮放。

### clipBorders
如果在虛擬螢幕有圖片或是物件顯示超出邊界範圍，KorGE的```clipBorders=true```預設是為直接切掉不顯示，如果把物件的座標設為虛擬螢幕外，是會看不見的。
我們拿先前在練習Sprite Animations的GamePlay.kt綠色外星人故意把初始x座標設為-100來做移動，其實你看他在背景範圍外不會顯示出來，直到移動到原點後才冒出來，這就是```clipBorders=true```的效果
```
alien.tween(alien::x[-100.0, 512.0], time = 3000.milliseconds)
```

![](https://cdn-images-1.medium.com/max/640/1*zoST-SteHgFWFPXsTZTueQ.gif)

如果遊戲有特別設計的需求，希望是超出虛擬螢幕外也希望能看見物件，可以將設定為```clipBorders=false```。
```
object ConfigModule : Module() {
        override val clipBorders = false
}
```
![](https://cdn-images-1.medium.com/max/640/1*6GWtgrGl27OtD9TfbitDEw.gif)

### 結論
會這樣區分實際螢幕跟虛擬螢幕主要也是要讓遊戲畫面去適合不同尺存的螢幕解析度，因為KorGE支援在不同平台的輸出，能跑在桌機上、Web及手機，所以才有virtual screen虛擬畫面的設計。至於怎麼輸出到不同平台的我們也會在往後的文章提到，這次就動手練習將你想要設計的遊戲螢幕大小囉！

### 參考文章
* [https://korlibs.soywiz.com/korge/reference/resolutions/](https://korlibs.soywiz.com/korge/reference/resolutions/)
