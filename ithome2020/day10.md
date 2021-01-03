# [Day10] 使用Input輸入系統
前面幾篇都像是在介紹透過畫面視覺呈現來給玩家刺激，但是大部分遊戲裡的互動，幾乎都還是由玩家來主動進行操控。

常見的行為就是透過滑鼠、鍵盤、或是遊戲搖桿，有的遊戲也有透過麥克風來進行聲控，若是手機遊戲，最直接的就是螢幕觸控，再來就是利用三軸加速來實現的遊戲類型，還有用鏡頭偵測影像來設計的遊戲！

KorGE當然也有提供這些從外部接收使用者輸入的Input類別，這一回筆者只嘗試了滑鼠跟鍵盤，這些可以直接在開發電腦上比較快進行示範的輸入類型來練習(手機的觸控螢幕要再後面一點發佈到手機平台上測試再來實驗)。

### 偵測Mouse滑鼠座標
取用輸入系統的方式很簡單，只要呼叫以下這行，就能取用正在執行程式的所有輸入。
```
val input = views.input
```
我們寫一個測試範例，將接受到的滑鼠座標數值透過Text物件印在畫面上，將input.mouse的x跟y數值印出來。
```
text("").addUpdater {
    text = "Mouse Position:${input.mouse}"
    position(input.mouse.x, input.mouse.y)
}
```
程式執行，滑鼠指標移到畫面隨意移動，程式都能偵測並印出目前所在位置。

![https://yayachang.github.io/ithome2020/images/day10-01.png](https://yayachang.github.io/ithome2020/images/day10-01.png)

### 偵測Key鍵盤輸入
鍵盤輸入的狀態分為三種，```justPressed、pressing跟justReleased```，就是一開始被按到，持續按跟按了放開。這裡我們寫了偵測鍵盤的空白鍵來測試。
```
text("").addUpdater {
    when {
        input.keys.justPressed(Key.SPACE) -> {
            println("Key justPressed")
        }
        input.keys.pressing(Key.SPACE) -> {
            println("Key pressing")

        }
        input.keys.justReleased(Key.SPACE) -> {
            println("Key justReleased")
        }
    }
}
```
程式執行後試著按下鍵盤上的空白鍵再放開，三種狀態就會在console上印出來，如果空白鍵按得越久，key pressing就會被持續偵測印出訊息。

**Event-based High-level API**
KorGE也有在View物件提供比較High-level的做法，最常使用的就是onClick這個方法了，所以滑鼠的跟鍵盤的事件偵測當然也少不了。
滑鼠動作可以寫成以下樣子:
```
mouse{
    click{
        println("mouse click")
    }
    over{
        println("mouse over")
    }
    down{
        println("mouse down")
    }
    up{
        println("mouse up")
    }
}
```
而鍵盤一樣可以改寫：
```
keys{
    down(Key.SPACE){
        println("Key down")
    }
    up(Key.SPACE){
        println("Key up")
    }
}
```

### 總結
學習這些Input相關資料當然就是要應用在遊戲裡頭囉，如果你的遊戲是需要角色的移動由玩家操控，你就要偵測上下左右的鍵盤了。而筆者特別挑了偵測空白鍵就是想要拿空白鍵當作外星人跳耀的觸發按鍵啦！怎麼實作出來，我們會再後面講說角色部分的時候再來拿來應用囉！

### 參考文章
* [https://korlibs.soywiz.com/korge/reference/input/](https://korlibs.soywiz.com/korge/reference/input/)
