# [Day07] 製作簡單的動畫效果
前面幾篇都是在介紹遊戲比較靜態的應用，這次終於要讓遊戲裡的東西開始動起來了！ 所以這一回的重點會是介紹大家怎麼在KorGE裡讓你的遊戲物件透過程式進行變化。

### while (true) + delay
這是官方文件介紹其中一個的簡單實現動畫效果的方式，就是在一個迴圈內實作你要想要的效果。

這時就聯想到我們在[Day04](https://yayachang.github.io/ithome2020/day4)時有學到Image的alpha值變化以及前兩回[Day05](https://yayachang.github.io/ithome2020/day5)跟[Day06](https://yayachang.github.io/ithome2020/day6)有一個進版提示文字"Tap to Start"，讓這個提示文字透過alpha值的變化，達到漸變消失又再次顯示的效果，進而更吸引玩家的注意!

記得先找出前一回練習的程式碼，然後把套用字型的"Tap to Start"的image物件宣告一個tapString變數，好讓在```while(true)+delay```區塊內進行操作。

```
//...前略... 請回Day06找程式碼。
val tapString = image(bitmap) {
    position((image.scaledWidth) / 2 - (textString.count() * fontSize) / 4, (image.scaledHeight - image.scaledHeight / 4))
    onClick {
        launchImmediately { sceneContainer.changeTo<Menu>() }
    }
}

launchImmediately {
    while (true) {
        tapString.alpha -= 0.1
        if(tapString.alpha <= 0){
            tapString.alpha = 1.0
        }
        delay(100.milliseconds)
    }
}
```
程式邏輯就是將tapString每100毫秒會減少0.1的alpha值，當小於等於0時又會恢復成alpha值等於1。
![](https://cdn-images-1.medium.com/max/640/1*FnfZ5GSmMbSM1aH8-0geHw.gif)

### addFixedUpdater
另一種實現動畫特效的方法是```addFixedUpdater```，是KorGE的view元件都有這一個method，你可以呼叫時帶入要更新的頻率，所以我們可以將```while(true)+delay```的程式進行改寫如下: 
```
tapString.addFixedUpdater(100.milliseconds){
    tapString.alpha -= 0.1
    if(tapString.alpha <= 0){
        tapString.alpha = 1.0
    }
}
```
將aplha變化的程式邏輯寫到```addFixedUpdater```內，然後每100毫秒更新頻率帶入，會得到一樣的效果唷！
既然已經學會怎麼在```addFixedUpdater```內實作動畫特效了，我們再多學一個將tapString做從上往下的垂直移動的效果，也就是讓物件的y座標從0然後移動到目前的顯示的位置。
```
var moveHeight =  (image.scaledHeight - image.scaledHeight / 4)

val tapString = image(bitmap) {
    position((image.scaledWidth) / 2 - (textString.count() * fontSize) / 4, 0.0)
    onClick {
        launchImmediately { sceneContainer.changeTo<Menu>() }
    }
}

tapString.addFixedUpdater(100.milliseconds){
    tapString.alpha -= 0.1
    if(tapString.alpha <= 0){
        tapString.alpha = 1.0
    }
    if(tapString.y < moveHeight){
        tapString.y += 30
    }
}
```
將要移動到的高度從position的y座標提出來，取名為moveHeight，然後position初始的時候y座標設為0，接下來只要在```addFixedUpdater```內加一個邏輯判斷，當y座標還是小於moveHeight，我們就持續將y座標增加30單位。
![](https://cdn-images-1.medium.com/max/640/1*3FCiZ8qAW2hdSKB2GapI9g.gif)

### addHrUpdater
另一種跟addFixedUpdater很像的是```addHrUpdater```，根據我的理解是根據螢幕畫面更新頻率去更新，而addFixedUpdater會是你給個固定頻去更新，所以兩者實際去實作出來後addHrUpater在做物體移動時會比較順暢。

### 總結
學會了這三招後，你就可以任意地對你遊戲內的物件要做形變或是位移以及旋轉都可以實現了。之後會有其他動畫效果的介紹，不過這一回我們就先學會最簡單動畫實作的方式，趕緊動手玩玩看囉！

### 參考文章
* [https://korlibs.soywiz.com/korge/reference/animation/#while-true--delay](https://korlibs.soywiz.com/korge/reference/animation/#while-true--delay)
* [https://korlibs.soywiz.com/korge/reference/animation/#addfixedupdater-and-addhrupdater](https://korlibs.soywiz.com/korge/reference/animation/#addfixedupdater-and-addhrupdater)
