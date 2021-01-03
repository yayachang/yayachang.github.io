# [Day08] 繼續學習動畫特效
[前一篇](https://yayachang.github.io/ithome2020/day7)已經學會用```while(true)+delay```以及```addFixedUpdater```跟```addHrUpdater```來製作一些簡易的動畫特效，不過KorGE還有提供其他方法來更容易實現動畫特效，分別是```Tweens跟Animator```，就讓筆者帶大家一起來學習了。

### Tweens
依照字面上的理解，Tween意思是8~10歲的少年，好像也有說法是來是從between來的，其實主要表達就是兩者之間的意思，而遊戲也有稱作圍"補間動畫"。
KorGE也針對了View物件提供了Tween這個方法，也就是View.Tween，據官方說法是讓你有magic的感受! 
確實使用起來也真的非常簡單，以官方提供的範例是將你的物件進行水平移動，只要給予物件的屬性的初始跟結束的數值，再設定更新頻率，就能達成效果了。
```
view.tween(view::x[10.0, 100.0], time = 1000.milliseconds)
```
所以我們可以用tween的方式改寫前一篇用addFixedUpdater裡頭實作的alpha值變化，直接一行搞定。
```
tapString.tween(tapString::alpha[0.0, 1.0], time = 1000.milliseconds)
```

### Animator
如果你不想把你的動畫行為分散地寫在物件上，你可以試試看用Animator來將這些動態行為寫在一起：
```
animate {
    parallel {
        tapString.moveToWithSpeed((image.scaledWidth) / 2 - (textString.count() * fontSize) / 4, (image.scaledHeight - image.scaledHeight / 4), 300.0, Easing.EASE_IN_OUT)
    }
    parallel{
        tapString.alpha(0.0, time = 1000.milliseconds, easing = Easing.LINEAR)
        if (tapString.alpha == 0.0) {
            tapString.alpha = 1.0
        }
    }
}
```
這裡的parallel代表一個Animator，使用moveToWithSpeed，將tapString的y座標從原點用300毫秒移動速度到指定位置。這裡會等第一個Animator動作完後，才會進行到下一個parallel，也就是對tapString做alpha值的變化。```tapString.alpha(0.0, time = 1000.milliseconds, easing = Easing.LINEAR)```。
執行後看到的結果跟我們幾乎一樣呢！！

![](https://cdn-images-1.medium.com/max/640/1*EoaSpLCgKemuYpLTXh0EZw.gif)

### 總結
動畫特效的寫法有很多種，光是讓物體移動就能有這麼多招式，如果你去看Animator.kt裡還有滿多可以使用的方法，像是moveTo、scaleTo、rotateTo等等。希望大家都能多去嘗試利用各種組合，試出你想要在遊戲中表現的特效！

### 參考文章
* [https://korlibs.soywiz.com/korge/reference/animation/#tweens](https://korlibs.soywiz.com/korge/reference/animation/#tweens)
