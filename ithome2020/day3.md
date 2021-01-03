# [Day03] 使用Scene切換畫面
在[前一篇](https://yayachang.github.io/ithome2020/day2)我們介紹了遊戲架構，分別有Splash、Menu、GamePlay、 GameOver、Rank五個遊戲畫面，所以我們今天要學習的部分就是先把遊戲的全部畫面的空殼建立起來，先讓畫面的殼能按照我們期望的遊戲順序顯示出來。

這些畫面在遊戲中我們有時候又會稱作Scene，也就是場景，有時候遊戲也會製作很多關卡，就會製作不同的Scene場景來載入，所以通常不會一個場景一鏡到底，都會依需求去切換到想要呈現的畫面。

在KorGE裡也有提供這樣的類別Scene，讓你能遊戲中使用不同的場景進行切換。
https://korlibs.soywiz.com/korge/reference/scene/

### 新建場景
這時候我們就可以開始動手開工了，你可以在IntelliJ的專案資料夾/src/commonMain/kotlin/ 再新建一個資料夾命名為 scene，把畫面相關的檔案都放在/src/commonMain/kotlin/scene，然後按下滑鼠右鍵
```New →Korge Scene →New Korge Scene →輸入檔名```

![https://yayachang.github.io/ithome2020/images/day03-01.png](https://yayachang.github.io/ithome2020/images/day03-01.png)

![https://yayachang.github.io/ithome2020/images/day03-02.png](https://yayachang.github.io/ithome2020/images/day03-02.png)

依序建立五個場景畫面：Splash、Menu、GamePlay、 GameOver、Rank

![https://yayachang.github.io/ithome2020/images/day03-03.png](https://yayachang.github.io/ithome2020/images/day03-03.png)

### Splash.kt
我們就先挑第一個開頭產景Splash來說明，由IntelliJ IDE產生出來的場景程式碼會自動繼承Scene()，還有順便帶入負責進行初始話的函式fun Container.sceneInit()，而程式碼的註解也告訴你，場景的初始就寫在這裡頭了。
```
package scene

import com.soywiz.korge.scene.Scene
import com.soywiz.korge.view.Container

class Splash(
) : Scene() {
    suspend override fun Container.sceneInit() {
        // @TODO: Scene initialization here
    }
}
```
但是光產生場景還是不行，因為還記得我們的程式入口是從main.kt開始，所以我們要來開始改寫main.kt的程式，讓程式的進入點改為Splash場景。

**重寫main.kt**
```
import com.soywiz.korge.Korge
import com.soywiz.korge.scene.Module
import com.soywiz.korim.color.Colors
import com.soywiz.korim.color.RGBA
import com.soywiz.korinject.AsyncInjector
import com.soywiz.korma.geom.SizeInt
import scene.*


suspend fun main() = Korge(Korge.Config(module = ConfigModule))

object ConfigModule : Module() {
    override val bgcolor: RGBA = Colors["#2b2b2b"]
    override val size = SizeInt(512, 512)

    override val mainScene = Splash::class

    override suspend fun AsyncInjector.configure() {
        mapPrototype { Splash() }
        mapPrototype { Menu() }
        mapPrototype { GamePlay() }
        mapPrototype { GameOver() }
        mapPrototype { Rank() }
    }
}
```
(1) 將第一天Day01寫的範例進行改寫

```Korge(width = 512, height = 512, bgcolor = Colors["#2b2b2b"])```

因為Korge可以藉由Korge.Config傳入一個Module來設定這些視窗的屬性。因此改寫成以下:

```Korge(Korge.Config(module = ConfigModule))```

(2)把視窗的屬性，全部放在ConfigModule去設定，會是跟第一天寫的範例達成一樣的效果
```
object ConfigModule : Module() {
    override val bgcolor: RGBA = Colors["#2b2b2b"]
    override val size = SizeInt(512, 512)
}
```

(3)把mainScene這個入口Scene設定給Splash::class，這樣開始執行後才會幫我們導向Splash場景

```override val mainScene = Splash::class```

(4) 使用AsyncInjector.configure()，算是KorGE幫忙寫好的一個依賴注射的方法，讓你不用特別每個場景都要額外去再進行建立新的object的動作，所以照著官方做法，將每個場景都用mapPrototype加進去，讓這些場景被呼叫時，就會自動幫你生成需要的物件了。
```
override suspend fun AsyncInjector.configure() {
        mapPrototype { Splash() }
        mapPrototype { Menu() }
        mapPrototype { GamePlay() }
        mapPrototype { GameOver() }
        mapPrototype { Rank() }
    }
```

### 練習切換Scence
前面一個步驟已經將main.kt重寫讓程式入口導入到Splash了，這時候我們來試著在每個場景都加一個按鈕來進行場景的切換，就能先假裝我們好像真的已經打開遊戲，重投玩到尾的感覺(只是裡頭完全還沒內容就是了…)

(1)將所有場景都加上文字跟按鈕，以Splash.kt為例
```
class Splash : Scene() {
    val textPos = Point(128, 128)
    val buttonWidth = 256.0
    val buttonHeight = 32.0
    val buttonPos = Point(128, 128 + 32)

    override suspend fun Container.sceneInit() {
        
        text("I'm in ${Splash::class.simpleName}") {
            position(textPos)
        }
        
        textButton(buttonWidth, buttonHeight) {
            text = "Go to Menu"
            position(buttonPos)
            onClick {
                launchImmediately { sceneContainer.changeTo<Menu>() }
            }
        }
    }
}
```
(2)透過文字來顯示目前是在哪一個Scene，我們使用text類別，也要告訴KorGE座標位置 position(textPos)。
```
val textPos = Point(128, 128)
text("I'm in ${Splash::class.simpleName}") {
            position(textPos)
        }
```
(3)透過按鈕的觸發，去切換到下一個要去Scene，這裡始用textButton，一樣也要幫按鈕命名，titile="Go to Menu"，一樣要給座標位置position(buttonPos)，最後onClick的觸發動作將場景切給Menu。
```
textButton(buttonWidth, buttonHeight) {
    text = "Go to Menu"
    position(buttonPos)
    onClick {
        launchImmediately { sceneContainer.changeTo<Menu>() }
    }
}
```
(4)執行```mygame →Tasks →korge-run →runJvm```
看到我們在程式寫的text跟button都顯示在畫面上了！也告訴你正在Splash場景。

![https://yayachang.github.io/ithome2020/images/day03-04.png](https://yayachang.github.io/ithome2020/images/day03-04.png)

(5)按下Go to Menu按鈕，進到下一個Menu場景，看到顯示I'm in Menu，而按鈕也改為顯示Go to GamePlay了！

![https://yayachang.github.io/ithome2020/images/day03-05.png](https://yayachang.github.io/ithome2020/images/day03-05.png)

(6)最後全部場景都改寫好的執行結果!

![](https://yayachang.github.io/ithome2020/images/day03-06.gif)

### 總結
以```Splash 開始，Menu → GamePlay → GameOver →Rank``` 再回到Menu這樣的循環。也就會呼應我們前一篇介紹的遊戲架構的流程。

![https://yayachang.github.io/ithome2020/images/day03-07.png](https://yayachang.github.io/ithome2020/images/day03-07.png)

最後應該只要示範Splash.kt的程式就行了吧，相信Menu切換到GamePlay之後的遊戲流程各位都能以此類推。之後的教學將會慢慢在各個場景上加上遊戲需要的東西囉！

### 參考資料
* [https://korlibs.soywiz.com/korge/reference/scene/](https://korlibs.soywiz.com/korge/reference/scene/)


