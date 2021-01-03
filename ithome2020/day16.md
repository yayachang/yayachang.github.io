# [Day16] GamePlay設計-角色走跳

不曉得各位還記得[Day9](https://yayachang.github.io/ithome2020/day09)我們學過用SpriteAnimations來做會走路的綠色外星人嗎？
是的，這次我們要寫程式來設計角色的內容了，首先我們要來定義外星人的狀態，會有走路、跳耀，掉落，受傷，死亡，跟過關這幾個狀態。

所以設計一個狀態的enum class如下：
```
enum class STATUS {
    WALK,
    JUMP,
    FALL,
    HURT,
    DEAD,
    GOAL
}
```
我們把外星人設計一個類別class Alient繼承Container，因為也要加到GamePlay的Scene，初始狀態會給走路STATUS.WALK。其餘載入素材的部分，就從Day9我們寫好的程式碼複製過來，走走走！

![https://yayachang.github.io/ithome2020/images/day16-01.png](https://yayachang.github.io/ithome2020/images/day16-01.png)

### Aline.kt 載入走路動畫程式碼
```
class Alien: Container() {

    var status = STATUS.WALK
    lateinit var spriteMap: Bitmap
    lateinit var walkAnimation: SpriteAnimation
    lateinit var walkSprite: Sprite

    suspend fun load() {
        spriteMap = resourcesVfs["green_alien_walk.png"].readBitmap()
        walkAnimation = SpriteAnimation(
                spriteMap = spriteMap,
                spriteWidth = 72,
                spriteHeight = 97,
                marginTop = 0,
                marginLeft = 0,
                columns = 11,
                rows = 1,
                offsetBetweenColumns = 0,
                offsetBetweenRows = 0
        )
        sprite = sprite(walkAnimation) {
            spriteDisplayTime = 0.1.seconds
        }

    }
```
再來我們要對外星人多加一個跳耀動作，做到跳的動作基本上只要一張圖就能做效果，當然你也可以有多一兩張的圖片做起來更生動囉。

在Allen.kt 的 load()部分我就會在多一張jump的圖片，跳跳跳！
![https://yayachang.github.io/ithome2020/images/day16-02.png](https://yayachang.github.io/ithome2020/images/day16-02.png)

### Aline.kt 載入跳的圖片程式碼
```
lateinit var jumpBitmap: Bitmap
suspend fun load() {
   jumpBitmap = resourcesVfs["green_alien_jump.png"].readBitmap()
}
```
圖片跟動畫載入完了，接下來加上外星人的走路跟跳耀動作，walk()跟jump()

### Aline.kt 走路的程式碼
狀態會變為WALK，然後walk的播放速度就是0.1秒換下一張，然後直接重複播放
```
fun walk() {
    status = STATUS.WALK
    walkSprite = sprite(walkAnimation) {
            spriteDisplayTime = 0.1.seconds
        }.apply {
            playAnimationLooped()
    }
}
```
### Aline.kt 跳耀的程式碼
跳耀需要不是正在跳也不是在跳落的狀態，才會改變狀態成跳耀的狀態，然然sprite變成跳耀的圖片。
```
fun jump() {
    if (status != STATUS.JUMP && status != STATUS.FALL) {
        changeStatus()
        sprite = sprite(jumpBitmap)
        status = STATUS.JUMP
    }
}
```
### Alien.kt狀態改變的程式碼
因為換圖片需要先把動畫停止，跟把圖片移掉，才能更換成其他圖片顯示，沒做這個動作的時候，發現走路圖片跟跳耀圖片一直會疊加上去，所以就特別加了這段程式。
```
fun changeStatus(){
    sprite.stopAnimation()
    sprite.removeFromParent()
}
```
### Alien.kt掉落的程式碼
當外星人跳到最高點要掉下去時，狀態改變成掉落。
```
fun fall() {
    status = STATUS.FALL
}
```
### Alien.kt狀態更新程式碼
每次更新時，若在跳躍狀態會減少垂直y數值，直到比預設值還少100時會準備進到掉落狀態，而到了跳落狀態，會一直增加垂直y數值，直到掉回地平面上，然後回到走路狀態。
```
fun update() {
    when (status) {
        STATUS.JUMP -> {
            y = y - 4
            if (y <= defaultHeight - 100) {
                fall()
            }
        }
        STATUS.FALL -> {
            y = y + 6
            if (y >= defaultHeight) {
                changeStatus()
                walk()
            }
        }
    }
}
```
### 回到GamePlay.kt程式碼開始初始化加入東西
終於開始慢慢把遊戲物件加入，記得物品一開始都放在sceneInit()裡，前一篇練習的背景跟物件也一起加進來。
輪到外星人給加到Scene裡時，把外星人的位置相對於地板的物件上，這樣就不用特別計算外星人在地板上的位置，然後記住外星人的預設地板高度defaultHeight，讓外星人才知道掉回地板位置，接著把外星人放在離左邊畫面的一個單位位置，看起來比較順眼。
```
lateinit var alien: Alien
lateinit var background: Background
override suspend fun Container.sceneInit() {

    //加入背景
    background = Background().apply { load() }
    addChild(background)

    //加入地板、障礙物、敵人
    ItemManager.init()
    ItemManager.load(this)

    //加入外星人
    alien = loadCharacter()
    addChild(alien)
    alien.alignBottomToTopOf(ItemManager.BASE_FLOOR!!)
    alien.defaultHeight = alien.y
    alien.x = ItemManager.BASE_WIDTH * 1

}
```
### GamePlay.kt程式碼偵測外星人跳躍
然後SceneMain()加上空白鍵盤的偵測，按下down會呼叫外星人的alien.jump()跳耀動作！
```
override suspend fun Container.sceneMain() {

    keys {
        down(Key.SPACE) {
            alien.jump()
        }
    }

    addHrUpdater {
        alien.update()
    }
}
```
Alien的狀態更新檢查update()會放在GaemPlay的addHrUpdater()去更新，最後如果要有跑酷的效果(可能有人要吐槽是走酷，因為外星人的素材只有走路…)，只要把金幣跟地板還有敵人move() 也放到addHrUpdater，就會一起動起來了！
```
addHrUpdater {
    ItemManager.move()//金幣、敵人、地板都放在這裡管理，所以一起移動
    alien.update()
}
```

執行程式的結果！ 外星人又走又跳囉！！！！
![](https://cdn-images-1.medium.com/max/640/1*LLMDqARDvzvhTm495PaLnQ.gif)

### 總結
基本上遊戲的玩法已經寫得五成左右了，相信大家一定還覺得這個Demo哪裡怪怪的！？沒錯，吃到金幣怎麼沒有消失呢，碰到敵人也沒消失，還有也沒有記到外星人的吃的金幣數等等資訊，所以下一回跟下下回我們就會開始介紹遊戲畫面上的UI顯示資訊跟怎麼處理角色跟物件接觸的情況了！
