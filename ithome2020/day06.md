# [Day06] 使用Font改變文字風格
相信有開發UI經驗的工程師們，當你們把成品拿給設計或是PM同事，還滿多第一時間都會反應，"那個字體也太醜了吧！！"之類的話，或是設計師有的會抱怨說，為什麼沒用我設計圖上指定的字型呢!?

工程師也是有點無奈，因為通常第一要務是功能有準確的呈現，UI的呈現優先權總是排在稍微後面，所以直接用系統上預設的字型來Demo呈現才是最快的。但是有時候是因為設計師根本沒有提供字型檔案(有時候是設計軟體裡的字型，實際上輸出到軟體上使用可能都要付費之類的)，那這時候就找要出錢的人討論拉?!

以上閒聊稍微偏離主題，現在回歸正題，如果我們有好看的設計字型何不拿來使用呢？！而且遊戲在視覺呈現上是非常重要的課題，雖然筆者現在都用醜醜的自畫像來Demo給大家看，但是不表示挑選適合遊戲本身主題的美術風格跟字型是不重要的喔，那是因為筆者的技能點不在美術，還是只能用工程師的風格，請多包涵。

KorGE裡頭也提供了使用自訂字型的功能，共有三種類型: BitmpFont、Device fonts、跟TTF fonts。筆者目前也只試出了TTF fonts，所以我們就先試著使用TTF fonts來替換目前的KorGE預設的字型。

### 下載免費開源字型

https://www.google.com/get/noto/
Google有提供Noto的免費開源字型，所以去找一個你想要的字型，然後解壓縮後，可以看到很多的ttf檔案，我挑了其中一種NotoSans-Black.ttf的字型來拿測試使用。

![https://yayachang.github.io/ithome2020/images/day06-01.png](https://yayachang.github.io/ithome2020/images/day06-01.png)

### 讀取字型檔
先把NotoSans-Black.ttf檔案放到專案資料夾的src/commonMain/kotlin/resources/裡頭。

![https://yayachang.github.io/ithome2020/images/day06-02.png](https://yayachang.github.io/ithome2020/images/day06-02.png)

可以開始動手寫Code了，在Splash.kt加上讀取字型檔的程式。
```
val ttfFont = TtfFont(resourcesVfs["NotoSans-Black.ttf"].readAll().openSync())
```
讀取後，因為字型檔在KorGE裡會變成要畫在一張bitmap上才能呈現在畫面上，所以接下來的程式會要額外加一些處理。
```
val textString = "Tap to Start"
val fontSize = 30

val bitmap = NativeImage(width = 200, height = 100).apply {
    getContext2d().fillText(textString,
            x = 0,
            y = fontSize,
            font = ttfFont,
            fontSize = fontSize.toDouble(),
            color = Colors.BLUE)
}
```
宣告一個textString並給字串值"Tap to Start"，然後字型大小fontSize為30，接著寫一個NativeImage的物件，寬=200，高=100，接下來再呼叫getContext2d().fillText這個方法把字串畫上，並指定剛剛讀取的字型，並指定為藍色字。
```
getContext2d().fillText(textString,
            x = 0,
            y = fontSize,
            font = ttfFont,
            fontSize = fontSize.toDouble(),
            color = Colors.BLUE)
```
*＊注意：依照官網的範例，照理應該是y要設定為0，但是不確定fillText這裡的座標起始的是從NativeImage的哪裡開始畫，設為0時，字串會沒辦法顯示出來，而筆者為了要讓字顯是出來，試出要將y設定成跟字型大小一樣的高度才會出現字串…。(這部分還需要釐清)*

### 顯示套用字型檔的字串
把已經建立好的字串bitmap物件，放到image物件裡頭，然後一樣設定位置跟onClick的行為。
```
image(bitmap) {
    position((image.scaledWidth) / 2 - (textString.count() * fontSize) / 4, (image.scaledHeight - image.scaledHeight / 4))
    onClick {
        launchImmediately { sceneContainer.changeTo<Menu>() }
    }
}
```
執行後，果然跟[上一回](https://yayachang.github.io/ithome2020/day5)的Text預設字串是不一樣的風格，是後來套用的NotoSans-Black字型。

![https://yayachang.github.io/ithome2020/images/day06-03.png](https://yayachang.github.io/ithome2020/images/day06-03.png)

### 總結
套用自訂的字型檔案步驟雖然多了一些，不過能選到符合遊戲風格的字型讓玩家感受到整體的一致性，都算是遊戲製作團隊的用心。趁這回來練習套用你喜歡的字型吧!

### 參考文章
* [https://korlibs.soywiz.com/korge/reference/font/](https://korlibs.soywiz.com/korge/reference/font/)
