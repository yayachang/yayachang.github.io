# [Day19] Splash進板畫面跟Menu遊戲大廳設計
在最近的日子比較著重在GamePaly畫面的設計，當然這部分是遊戲的重心，不過KorGE這一系列累積的18天練習應該可以應付大部分的小遊戲設計囉！但是我還是要把剩下的畫面建構起來，才能是個完整的遊戲。

### 遊戲的門面Splash
如果大家在[Day13-遊戲背景製作](https://yayachang.github.io/ithome2020/day13)有印象的話，我們選用了一張具有冒險動作遊戲的氣氛的圖片，所以只要在Splash.kt裡修改載入的圖片，就能完成進版畫面了!

![https://yayachang.github.io/ithome2020/images/day19-01.png](hhttps://yayachang.github.io/ithome2020/images/day19-01.png)

但是有KENNEY大大的LOGO在那裏(用佛心的免費開源圖片而且修改也不用額外說明跟付授權費，這應該要多幫忙宣傳)，如果你也喜歡這類的風格素材也可以購買其它需要付費的素材。

圖片素材網站:https://kenney.nl/assets/platformer-art-deluxe

![https://yayachang.github.io/ithome2020/images/day19-02.png](https://yayachang.github.io/ithome2020/images/day19-02.pngg)

既然可以原作者允許修改原圖，我就可以試著用GIMP免費影像處理程式來把LOGO去掉，加一個自己取名的LOGO: ```ALIEN RUN```。

GIMP網站: https://www.gimp.org/

![](https://www.gimp.org/images/frontpage/wilber-big.png)

在自製小遊戲沒有美術支援，也只能先用不專業的土炮作法(若有朋友快去求救或是有讀友贊助幫忙P好看的LOGO)。改好後，再加上我們一開始的"Tap To Start"，就完成Splash畫面了。

![https://yayachang.github.io/ithome2020/images/day19-03.png](https://yayachang.github.io/ithome2020/images/day19-03.pngg)

### 遊戲大廳入口Menu
進入Menu畫面，就會有五隻不同顏色的外星人排排站，選其中一隻來進行遊戲。這裡我想要設計點一下外星人，外星人會開始原地走路，然後在畫面空白區會有外星人頭像出現，旁邊有一個GO的按鈕，按下後外星人就會往右走進入GamePlay遊戲畫面。

![https://yayachang.github.io/ithome2020/images/day19-04.pngg](https://yayachang.github.io/ithome2020/images/day19-04.png)

把剩下外星人的圖源準備好
(1)每個外星人的站立圖：

![https://yayachang.github.io/ithome2020/images/day19-05.png](https://yayachang.github.io/ithome2020/images/day19-05.png)

(2)紫色外星人、走路、跳躍、受傷：

![https://yayachang.github.io/ithome2020/images/day19-06.png](https://yayachang.github.io/ithome2020/images/day19-06.png)

(3)粉紅色外星人站立、走路、跳躍、受傷：

![https://yayachang.github.io/ithome2020/images/day19-07.png](https://yayachang.github.io/ithome2020/images/day19-07.png)

(4)米色外星人站立、走路、跳躍、受傷：(米色跟黃色的外星人走路的免費素材只有兩張，只有加減使用了．．．)

![https://yayachang.github.io/ithome2020/images/day19-08.png](https://yayachang.github.io/ithome2020/images/day19-08.png)

(5)黃色外星人站立、走路、跳躍、受傷：

![https://yayachang.github.io/ithome2020/images/day19-09.png](https://yayachang.github.io/ithome2020/images/day19-09.png)

### Alien.kt程式碼
加入角色的Enum Class
```
enum class CHARACTER {
    GREEN,
    PURPLE,
    PINK,
    BEIGE,
    YELLOW
}
```
然後載入圖片的時候帶入你在Menu選角色時數值，例如你選了粉紅色的外星人，就傳入CHARACTER.PINK。
```
var alienWalkCount = 11 //走路的圖片數
var alienWalkSpeed = 0.1 //每張走路圖片的播放秒數
suspend fun load(index:CHARACTER) {
    when(index){
        CHARACTER.GREEN->{
            //載入綠色外星人
        }
        CHARACTER.PURPLE->{
            //載入紫色外星人
        }
        CHARACTER.PINK->{ 
            //載入粉紅色外星人
            alienWalkCount = 11
            alienWalkSpeed = 0.1
            headBitmap = resourcesVfs["pink_alien_head.png"].readBitmap()
            standBitmap = resourcesVfs["pink_alien_stand.png"].readBitmap()
            spriteMap = resourcesVfs["pink_alien_walk.png"].readBitmap()
            hurtBitmap = resourcesVfs["pink_alien_hurt.png"].readBitmap()
            jumpBitmap = resourcesVfs["pink_alien_jump.png"].readBitmap()
        }
        CHARACTER.BEIGE->{
           //載入粉米色外星人
        }
        CHARACTER.YELLOW->{
            //載入黃色色外星人
        }
    }
```
走路跟受傷的SpriteAnimation也可以改成比較彈性的寫法，已經不用去指定某隻外星人的寬跟高，只要依讀取圖片的bitmap寬高來程式自動判斷決定。然後預設的顯示圖片就是站立的圖。
```
hurtAnimation = SpriteAnimation(spriteMap = hurtBitmap, spriteWidth = hurtBitmap.width, spriteHeight = hurtBitmap.height)
walkAnimation = SpriteAnimation(
        spriteMap = spriteMap,
        spriteWidth =  spriteMap.width/alienWalkCount,
        spriteHeight = spriteMap.height,
        marginTop = 0,
        marginLeft = 0,
        columns = alienWalkCount,
        rows = 1,
        offsetBetweenColumns = 0,
        offsetBetweenRows = 0
)
sprite = sprite(standBitmap)
```
然後為了要做到第一個點擊外星人播放走路，接著點其他外星人也要走路，那先點到的外星人就要停止變回站立動作，所以外星人多了一個fun stop()。
```
fun stop(){
    changeStatus()
    status = STATUS.STAND
    sprite = sprite(standBitmap)
}
```

### Menu.kt程式碼
外星人的程式碼準備好，就到Menu放背景圖跟一個提示玩家選擇外星人的字串"Choose An Alien to GO"，這部分幾乎跟Splash.kt的背景圖跟"Tap to Start"做法是一樣的，就讓大家自行練習(或是直接去看我的Github也行)。
接著加上五隻外星人，點擊onClick{}外星人會將上次選的外星人先暫停，然後這次選的外星人會開始走路。
```
var selectRunAlien: Alien? = null
var aliens = ArrayList<Alien>()
val greenAlien = Alien().apply {
    load(CHARACTER.GREEN)
    onClick {
        selectRunAlien?.stop()
        selectRunAlien = this
        walk()
    }
}
val purpleAlien = Alien().apply {}
val pinkAlien = Alien().apply {}
val beigeAlien = Alien().apply {}
val yellowAlien = Alien().apply {}
aliens.addAll(listOf(greenAlien, purpleAlien, pinkAlien, beigeAlien, yellowAlien))
```
把五隻外星人加到陣列後，就開始分配在外面上的位置，先把所有外星人的寬度加起來，然後用遊戲畫面的寬度去剪掉除以6個間隔，就能在外面上平均站立。
```
val bg = this
var totalAlienWidth = 0.0
aliens.forEach {
    totalAlienWidth += it.width
    addChild(it)
}

var alienSpace = (bg.width - totalAlienWidth) / (aliens.count() + 1)
```
然後只要把第一個綠色外星人在遊戲外面放好位置，接下來的外星人只要alignBottomToBottomOf跟alignLeftToRightOf用相對位置參考前一隻外星人就能排好了。
```
for (i in 0 until aliens.count()) {
    aliens[i].apply {
        when (i) {
            0 -> {
                aliens[i].alignBottomToBottomOf(bg)
                aliens[i].alignLeftToLeftOf(bg)
                y -= 42
            }
            else -> {
                aliens[i].alignBottomToBottomOf(aliens[i - 1])
                aliens[i].alignLeftToRightOf(aliens[i - 1])
            }
        }
        x += alienSpace
    }
}
```
最後的點擊了外星人，要在"Choose An Alien to Go"下更換外星人頭像跟一張Go的按鈕圖。
我們把判斷程式寫在addUpdater，這樣點擊頭像就會跟著被選的外星人頭像隨時更換，然後Go的按鈕就添加onClick{}動作會進到GamePlay畫面。
```
addUpdater {
    selectRunAlien?.also {
        if (headImage == null) {
            headImage = image(it.headBitmap) {
                anchor(0.5, 0.5)
                alignTopToBottomOf(tapString)
                centerXOn(bg)
                scale = 1.5
                x -= 200
                y += 10

            }
            goImage = image(goBitmp) {
                anchor(0.5, 0.5)
                alignTopToBottomOf(tapString)
                alignLeftToRightOf(headImage!!)
                centerXOn(bg)
                y += 10

                onClick {
                    launchImmediately { sceneContainer.changeTo<GamePlay>() }
                }
                launch {
                    while (true) {
                        delay(200.milliseconds)
                        tween(this::rotation[(-5).degrees], time = 100.milliseconds, easing = Easing.EASE_IN_OUT)
                        tween(this::rotation[(5).degrees], time = 100.milliseconds, easing = Easing.EASE_IN_OUT)
                    }
                }
            }

        } else {
            headImage!!.bitmap = it.headBitmap.slice()
        }
    }
}
```
為了Go看起來更明顯，就拿一開始KorGE的初始project學到的搖晃圖片程式
```tween(this::rotation[(-5).degrees], time = 100.milliseconds, easing = Easing.EASE_IN_OUT)```
 ```tween(this::rotation[(5).degrees], time = 100.milliseconds, easing = Easing.EASE_IN_OUT)
 }```
按Go之後，外星人往右移動走到GamePlay遊戲畫面，只要將選到的外星人的x軸往上加就能辦到囉！
```
addHrUpdater {
    if(statToGo) {
        selectRunAlien?.also {
            it.x+= 6
        }
    }
}
```
### Splash跟Menu畫面完成！跟GamePlay銜接了！
![https://cdn-images-1.medium.com/max/640/1*4gb475tfHH6gVH77RtUsLw.gif](https://cdn-images-1.medium.com/max/640/1*4gb475tfHH6gVH77RtUsLw.gif)

### 總結
相信遊戲畫面的部分各位會做得比筆者更好(因為筆者沒有去修練美術技能)，就只能拿現成的來充當遊戲素材，程式部分目前也沒有特別去管效能跟什麼厲害的原則，主要以能跑遊戲效果讓大家看，至少確認目前寫的Code是能執行成功的，再接下來一篇就是把GameOver畫面跟Rank畫面補齊囉！
