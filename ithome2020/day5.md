# [Day05] 使用Text處理文字
遊戲中除了會有圖片呈現，有一些文字在上面也是不可或缺的，雖然好的設計是不用文字言語就能讓玩家理解，但是有時一些簡單的文字輔助，還是讓玩家比較快能進入狀況。而且遊戲裡一些像是玩家名稱，或是一些程式版本號碼，也是由文字組成，因此文字的呈現還是必要的存在(雖然知道有的設計會連文字都直接用圖片替代)，這一回就來介紹KorGE裡的Text給大家了。

### 在Splash.kt加上提示字串
不知道大家有沒有印象，很多遊戲出現進版畫面後，其實會等玩家主動點及螢幕才真正進入遊戲？ 所以這次我們也要來練習這樣的做法，當進入到Splash時，我們也加一個字串來提示玩家要點擊畫面，才能繼續進到下一頁。
試著在程式裡頭加上下列的文字！
```
text("Tap to Start")
```
執行結果"Tap to Start"的文字出現在左上角了！

![https://yayachang.github.io/ithome2020/images/day05-01.png](https://yayachang.github.io/ithome2020/images/day05-01.png)

### 擺放Text位置
不過位置放在左上角實在是無法引人注意，還可能會被忽略掉，我們試著放在正中間，然後離下面高度1/4的位置，視覺上會比較好看到。
```
text("Tap to Start"){
    position((image.scaledWidth-scaledWidth)/2, (image.scaledHeight - image.scaledHeight/4))
}
```

![https://yayachang.github.io/ithome2020/images/day05-02.png](https://yayachang.github.io/ithome2020/images/day05-02.png)

### 加大Text跟加上顏色
嗯嗯，位置放對了，不過字體有點小，而且預設白色不是很明顯，我們試著改大小跟換個藍色的字來練習。
```
text("Tap to Start"){
    textSize = 30.0
    color = Colors.BLUE
    position((image.scaledWidth-scaledWidth)/2, (image.scaledHeight - image.scaledHeight/4))
}
```
執行後，加大跟換顏色的字體更大又更明顯了！

![https://yayachang.github.io/ithome2020/images/day05-03.png](https://yayachang.github.io/ithome2020/images/day05-03.png)

### 觸發onClick進到下一場景
既然我們都寫了Tap to Start，那就要真的來實作按下去就會跳進下一個Menu畫面了，我們在[Day03](https://yayachang.github.io/ithome2020/day3)的範例就有用按鈕去操作切換場景囉！不過KorGE所有的view物件都有onClick的功能，就是觸發點擊事件，所以這裡是拿Text再來做一次練習。
```
text("Tap to Start"){
    textSize = 30.0
    color = Colors.BLUE
    position((image.scaledWidth-scaledWidth)/2, (image.scaledHeight - image.scaledHeight/4))
    onClick {
        launchImmediately { sceneContainer.changeTo<Menu>() }
    }
}
```
當然不想要觸發的範圍只限制在Text上，你可以直接將onClick你放在Scene裡，這樣只要點到任何視窗上的範圍就會進到Menu了！
```
override suspend fun Container.sceneInit() {
    val image = image(resourcesVfs["mylogo.png"].readBitmap()) {...}
    text("Tap to Start"){...}
    onClick {
        launchImmediately { sceneContainer.changeTo<Menu>() }
    }
}
```
### 總結
這回的Text介紹應該輕鬆簡單就學起來了(跟上一篇Image比起來)，大家也可以試著把©copyright或是版本號放在進版畫面，讓自己的進版畫面真的有這麼一回事的感覺唷，這裡就交給大家自行練習了！

### 參考文章
* [https://korlibs.soywiz.com/korge/reference/views_standard/#text](https://korlibs.soywiz.com/korge/reference/views_standard/#text)
