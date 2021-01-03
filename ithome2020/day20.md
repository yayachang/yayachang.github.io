# [Day20] GameOver遊戲結算畫面設計

我們的小遊戲畫面設計已經快要接近尾聲了，也就就是要GameOver了!!(不是啦～是要來把GameOver畫面製作出來)
在[[Day13]遊戲背景製作](https://yayachang.github.io/ithome2020/day13)都已經有草稿示意圖出來了，就是闖關失敗跟成功各自會有不一樣的呈現畫面。

![https://yayachang.github.io/ithome2020/images/day20-01.png](https://yayachang.github.io/ithome2020/images/day20-01.png)

![https://yayachang.github.io/ithome2020/images/day20-02.png](https://yayachang.github.io/ithome2020/images/day20-02.png)

不過這GameOver的畫面的結果其實也是承接由GamePlay的遊戲結果，而GamePlay出來現身的外星人，是從Menu選擇出來的，所以這邊我們其實要有一個地方來記憶目前選到的外星人暫存，讓這個資料共享在不同的畫面當中。因此我在```src/commonMain/scene```的資料夾下產生一個```SharedData```來存放這些資料。

![https://yayachang.github.io/ithome2020/images/day20-03.png](https://yayachang.github.io/ithome2020/images/day20-03.png)

### SharedData.kt
分別的變數有選了那一個外星人，分數，跟是否闖關成功。
```
object SharedData {
    var SELECT_RUN_ALIEN:CHARACTER = CHARACTER.GREEN
    var GAME_SCORE:Int = 0
    var IS_GAME_OVER_SUCCESS = true
}
```
這樣在Menu.kt的外星人被點擊onClick部分就能加上SELECT_RUN_ALIEN是哪隻外星人了。也能在GamePlay.kt的載入角色時，知道是選了哪一隻。

### GamePlay.kt
```
suspend fun loadCharacter(): Alien {
    return Alien().apply {
        load(SharedData.SELECT_RUN_ALIEN)
        walk()
    }
}
```
接著我們在時間倒數完的時候，會結束遊戲，就當作闖關成功的條件，在血包扣光則是闖關失敗，同時外星人也會做出成功跟失敗的表情動作，場景物件也需要全部停止移動，SharedData的分數跟狀態也同時記錄下來要傳給下一個GameOver場景。

```
//進入遊戲結束畫面
fun checkGameOver(){
    if(gameTimer.totalTime == 0){
        background.stop()
        ItemManager.stop()
        alien.goal()//外星人成功動作
        SharedData.run {
            GAME_SCORE = score.nowValue
            IS_GAME_OVER_SUCCES = true
        }
        goToGameOver()
    }else if(blood.nowValue == 0){
        background.stop()
        ItemManager.stop()
        alien.dead()//外星人失敗動作
        SharedData.run {
            GAME_SCORE = score.nowValue
            IS_GAME_OVER_SUCCES = false
        }
        goToGameOver()
    }
}
```
```
fun goToGameOver() {
    launch {
        delay(2.seconds)//延遲兩秒讓外星人做動作
        sceneContainer.changeTo<GameOver>() 
    }
}
```
你瞧！粉紅外星人因為過關了正在手舞足道！(差一點要死掉了….)

![https://yayachang.github.io/ithome2020/images/day20-04.png](https://yayachang.github.io/ithome2020/images/day20-04.png)

反之，如果受傷失敗就會擺臭臉，血包都被扣光光了。

![https://yayachang.github.io/ithome2020/images/day20-05.png](https://yayachang.github.io/ithome2020/images/day20-05.png)

### GameOver.kt
在GameOver畫面用SharedData.IS_GAME_OVER_SUCCES判斷要顯示的背景畫面，跟外星人顯示的表情。
```
//闖關背景圖案
if(SharedData.IS_GAME_OVER_SUCCES){
    image(resourcesVfs["gameover_success.png"].readBitmap()) {
        anchor(0.5, 0.5)
        scale(ConfigModule.size.width / width, ConfigModule.size.height / height)
        position(scaledWidth / 2, scaledHeight / 2)
    }
}else{
    image(resourcesVfs["gameover_failed.png"].readBitmap()) {
        anchor(0.5, 0.5)
        scale(ConfigModule.size.width / width, ConfigModule.size.height / height)
        position(scaledWidth / 2, scaledHeight / 2)
    }
}
```
```
//外星人闖關後表情
val alien = Alien().apply { load(SharedData.SELECT_RUN_ALIEN)}
addChild(alien)
alien.alignTopToBottomOf(tapString)
alien.alignRightToLeftOf(score)
alien.x -=30
if(SharedData.IS_GAME_OVER_SUCCES){
    alien.goal()
}else{
    alien.hurt()
}
```
最後把分數跟闖關字串跟NEXT下一步放上遊戲畫面上。
```
//加入分數
val score = Score().apply { load() }
addChild(score)
score.initPosition()
score.alignTopToBottomOf(tapString)
score.nowValue = SharedData.GAME_SCORE
score.update()
```
```
//加上下一步按鈕
image( resourcesVfs["next.png"].readBitmap()) {
    anchor(0.5, 0.5)
    position(image.width / 2, image.height - 200)
    onClick {
        launch {
            sceneContainer.changeTo<Rank>()
        }
    }
}
```
把以上的程式碼補齊後，從GamePlay結束的兩秒後，就是延續GamePlay.kt的goToGameOver()這一段程式。
```
fun goToGameOver() {
    launchImmediately {
        delay(2.seconds)//延遲兩秒讓外星人做動作
        sceneContainer.changeTo<GameOver>() 
    }
}
```
場景畫面自動切到結算畫面，告知玩家闖關成功或失敗，顯示得到的分數，也有NEXT按鈕提醒到下一頁去。

![https://yayachang.github.io/ithome2020/images/day20-06.png](https://yayachang.github.io/ithome2020/images/day20-06.png)

![https://yayachang.github.io/ithome2020/images/day20-07.png](https://yayachang.github.io/ithome2020/images/day20-07.png)

### 總結
不知道有人發現我一開始設計的圖不是中文的闖關成功跟闖關失敗嗎? 
嗯嗯嗯..你沒看錯，原因是KorGE內建沒有支援中文字顯示，就先暫時換破英文來顯示了。
但是後來我還很很認真跑去github發了issue:https://github.com/korlibs/korge/issues/288
作者很快就告訴我原因，就是你要自己去弄中文字型，後來發現自己還滿犯蠢的應該要早點發現@@，大概都一直在開發APP，不管切換什麼語言字都能顯示出來，覺得應該隨便打字都要正常吧。所以補上一張闖關成功的截圖。 我是下載這套免費字型「內海字體」：https://github.com/max32002/naikaifont

![https://yayachang.github.io/ithome2020/images/day20-08.png](https://yayachang.github.io/ithome2020/images/day20-08.png)

下一回就要來開始設計除了核心遊戲外的小系統功能排行榜囉！
