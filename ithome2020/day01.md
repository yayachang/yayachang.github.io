# [Day01] 先學會安裝KorGE
### 前言
最近Kotlin很流行，可以拿來寫Android，也能拿來寫伺服器端的部分，像是Ktor，還有Spring boot也支援用Kotlin寫了。 曾經寫過遊戲程式的我心想，那有專門用Kotlin打造的遊戲引擎嗎? 結果搜尋了一下竟然有耶！
就是KorGE本人了！

Korge就是完全用Kotlin寫的，同時也支援多平台，你可以在Android, iOS, Web以及桌機都能開發。 
[https://korge.org/](https://korge.org/)
![https://yayachang.github.io/ithome2020/images/day01-01.png](https://yayachang.github.io/ithome2020/images/day01-01.png)

同時也非常好安裝使用，JetBrains的IntelliJ Plugin也有支援安裝KorGE的套件，所以省去要自己設定專案的時間，而且所有開發Debug的動作都能在IntelliJ完成， 對於要開發簡單輕量型的遊戲是很快速方便的，所以事不宜遲，就要來教大家行前安裝了！

### 安裝IntelliJ開發工具
請前往JetBrains官網找到IntelliJ，[https://www.jetbrains.com/idea/](https://www.jetbrains.com/idea/)
下載: IntelliJ IDEA Community 版本，初學用這個版本就夠了！

 ![https://yayachang.github.io/ithome2020/images/day01-02.png](https://yayachang.github.io/ithome2020/images/day01-02.png)
 
### 在IntelliJ安裝Korge Plguin
安裝完後，打開IntelliJ後，看到入口畫面，但先別急著New Project，我們要先在右下角點開Configure →Plugins進行KorGE的套件安裝

![https://yayachang.github.io/ithome2020/images/day01-03.png](https://yayachang.github.io/ithome2020/images/day01-03.png)

在Marketplace的搜尋列打上KorGE就可以看見Korge的套件，按下Install等待安裝好後，再重新啟用IntelliJ就能完成安裝了，也能開始進行建立新的Korge專案了！

![https://yayachang.github.io/ithome2020/images/day01-04.png](https://yayachang.github.io/ithome2020/images/day01-04.png)

### 開新的Korge專案
建立新專案(Create New Project)，點選視窗左邊的Korge，會出現四種常見的遊戲引擎套件，不過這次的介紹會以2D的遊戲為主(其餘有機會使用會再介紹)，所以我勾選了Box-2D Support，按下Next。

![https://yayachang.github.io/ithome2020/images/day01-05.png](https://yayachang.github.io/ithome2020/images/day01-05.png)

接著專案名稱自己取想要的mygame，然後下一步選專案放的位置，就完成專案建立了！

![https://yayachang.github.io/ithome2020/images/day01-06.png](https://yayachang.github.io/ithome2020/images/day01-06.png)
![https://yayachang.github.io/ithome2020/images/day01-07.png](https://yayachang.github.io/ithome2020/images/day01-07.png)

### 專案資料結構
展開Project的資料夾，可到有主程式入口src/commonMain/kotlin/main.kt跟需要的放置遊戲資源的/src/commonMain/resources/korge.png

![https://yayachang.github.io/ithome2020/images/day01-08.png](https://yayachang.github.io/ithome2020/images/day01-08.png)

### main.kt
用Korge Plugin產生的專案會給我們一個簡單的圖片顯示範例
```
suspend fun main() = Korge(width = 512, height = 512, bgcolor = Colors["#2b2b2b"]) {
   val minDegrees = (-16).degrees
   val maxDegrees = (+16).degrees

   val image = image(resourcesVfs["korge.png"].readBitmap()) {
      rotation = maxDegrees
      anchor(.5, .5)
      scale(.8)
      position(256, 256)
   }

   while (true) {
      image.tween(image::rotation[minDegrees], time = 1.seconds, easing = Easing.EASE_IN_OUT)
      image.tween(image::rotation[maxDegrees], time = 1.seconds, easing = Easing.EASE_IN_OUT)
   }
}
```
### 程式碼解說
這裡簡單說明這段程式碼的行為

```Korge(width = 512, height = 512, bgcolor = Colors["#2b2b2b"]){…}```
宣告了窗範圍width=512 height=512的長寬視窗，背景為黑色系的bgcolor=Colors["#2b2b2b"]

```image(resourcesVfs["korge.png"].readBitmap()){…}```
讀取resources放的korge.png圖片並前放置在中間

```image.tween(image::rotation[minDegrees], time = 1.seconds, easing = Easing.EASE_IN_OUT)```
放在while迴圈裡進行每秒正負16度旋轉。

### 編譯執行
接著可以在gradle大象找到mygame →Tasks →korge-run →runJvm

![https://yayachang.github.io/ithome2020/images/day01-09.png](https://yayachang.github.io/ithome2020/images/day01-09.png)

執行跑起來後，可以看到圖片左右來回旋轉的動畫效果唷！

![](https://yayachang.github.io/ithome2020/images/day01-10.gif)

### 自己動手做
看官網的KorGE範例圖當然是不過癮，當然置換成自己的設計的遊戲Logo！方法很簡單
(1)在/src/commonMain/resources/資料夾，新增自己的Logo，取名為mylogo.png
![https://yayachang.github.io/ithome2020/images/day01-11.png](https://yayachang.github.io/ithome2020/images/day01-11.png)

(2)在main.kt將"korge.png"取代換成"mylogo.png"
```val image = image(resourcesVfs["mylog.png"].readBitmap()){...}```

(3)再次執行mygame →Tasks →korge-run →runJvm
![](https://yayachang.github.io/ithome2020/images/day01-12.gif)

### 總結
實際一步一步操作應該感覺還滿簡單輕鬆的吧，所以學會安裝KorGE的大家，可以開始想想自己要動手做哪一款小遊戲，因為至少第一天你已經可以把自己設計的Logo展現出來了！
我接下來的文章也會慢慢介紹怎麼用KorGE提供的元件來實現遊戲設計中需要的元素唷。

### 參考資料
* [https://korlibs.soywiz.com/korge/gettingstarted/setup/](https://korlibs.soywiz.com/korge/gettingstarted/setup/)
* [https://www.youtube.com/watch?v=ANMiHx3z_No&feature=emb_logo](https://www.youtube.com/watch?v=ANMiHx3z_No&feature=emb_logo)
