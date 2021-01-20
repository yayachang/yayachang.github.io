# [Day15] GamePlay設計-背景、地面、物品
[上一篇](https://yunghsin.app/ithome2020/day14)介紹算是交代了我們GamePlay的畫面架構，這回我們真的要動手來寫Code，把遊戲的背景、還有地板、跟障礙物還有得分的物品一一放到遊戲畫面上去囉！

練習的時候可以把同個Scene的程式先放在一起，所以我多建立一個專門放Gameplay的資料夾```/src/commonMain/kotlin/gameplay```

首先這些物件我們建立一個抽象類別Item，因為遊戲上的這些物品都要加入Scene裡頭，所以都繼承了Container，讓Scence可以加這些個別物件都可以addChild加到View裡呈現，然後物件會load()載入跟move()移動的動作，還有moveSpeed移動速度跟初始X座標位置defaultX。

### Item.kt程式碼:
```
abstract class Item : Container() {
    var moveSpeed = 4
    var defaultX = 0.0
    abstract suspend fun load()
    open fun move() {
        x -= moveSpeed
    }
}
```
### 遊戲的背景
命名為Background，直接override load()跟move()動作即可。

### Background.kt程式碼:
```
class Background: Item() {
    override suspend fun load(){
        image(resourcesVfs["bg_shroom.png"].readBitmap())
    }
}
```
*回到GamePlay.kt的程式碼:*
這時已經可以先把先前寫在GamePlay的練習程式先移掉擺在一旁，我們要開始重新寫新的Code，所以只要保留sceneInit()的殼。我們在GamePlay.kt的sceneInit()裡進行背景圖的載入，並且讓背景加到GamePlay裡。
```
class GamePlay : Scene() {
    override suspend fun Container.sceneInit() {
        addChild(Background().apply { load() })
    }
}
```
執行後的結果會是這樣，會有底下黑色是正常的，因為你去看原圖大小是1024x512，我們的虛擬視窗大小是960x540，會少了28個單位，但是這不會特別影響我們最後遊戲畫面的呈現，因為我們接下來要畫外星人站在上面的地平面，他的高度會是70個單位，會遮掉黑色的部分，如果有強迫症一定不留白的人，可以自己去scale圖片或是找一張剛好適合高度的圖片囉。

![https://yayachang.github.io/ithome2020/images/day15-01.png](https://yayachang.github.io/ithome2020/images/day15-01.png)

### 地平面圖片
地板的貼圖不像幾乎佔滿整個遊戲場景的背景圖，只需要一個單位的貼圖，然後連續貼滿一行畫面，就能製造出地平面的效果了。

### Floor.kt程式碼:
寫法跟Background幾乎一樣了，就是換張圖片囉
```
class Floor (): Item() {
    override suspend fun load() {
        image(resourcesVfs["grassMid.png"].readBitmap())
    }
}
```
### 金幣圖片
金幣的效果就是我們的得分物件囉。

*Coin.kt程式碼:*
```
class Coin (): Item() {
    override suspend fun load() {
        image(resourcesVfs["hud_coins.png"].readBitmap())
    }
}
```
### 障礙物圖片
障礙物可能就像是石頭，角色撞到將會失分或是扣血

### Obstacle.kt程式碼:
```
class Obstacle (): Item() {
    override suspend fun load() {
        image(resourcesVfs["rock.png"].readBitmap())
    }
}
```
### 敵人Spreite Animation
敵人就跟角色一樣是有動畫特效，不過還是算在遊戲畫面中的物件，一樣繼承Item()

### Enemy.kt程式碼:
```
class Enemy: Item() {

    lateinit var spriteMap: Bitmap
    lateinit var walkAnimation: SpriteAnimation
    lateinit var walkSprite: Sprite

    override suspend fun load() {
        spriteMap = resourcesVfs["pink_enemy_walk.png"].readBitmap()
        walkAnimation = SpriteAnimation(
                spriteMap = spriteMap,
                spriteWidth = 51,
                spriteHeight = 28,
                marginTop = 0,
                marginLeft = 0,
                columns = 2,
                rows = 1,
                offsetBetweenColumns = 0,
                offsetBetweenRows = 0
        )
        walkSprite = sprite(walkAnimation) {
            spriteDisplayTime = 0.1.seconds
        }
        walkSprite.playAnimationLooped()
    }
}
```

### 回到GamePlay.kt的程式碼:
這邊我會先宣告一個存放整張畫面的陣列。
```
val stageValue = listOf<Int>(0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 2, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 2, 2, 2, 3, 2, 2, 4, 2, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1)
```

以及一個enum class，去表示對應的項目
```
enum class ITEM_TYPE {
    NONE,
    FLOOR,
    COIN,
    OBSTACLE,
    ENEMY
}
```
把畫面上的物品依stageValue去分配畫面上的位置
```
var index = 0
for (y in 0 .. 5) {
    for (x in 0..27) {

        val item = when (ITEM_TYPE.values()[stageValue[index++]]) {
            ITEM_TYPE.FLOOR -> {
                Floor().run {
                    defaultX = x*BASE_WIDTH
                    position( defaultX, y *BASE_HEIGHT + OFFSET)
                }
            }
            ITEM_TYPE.COIN -> {
                Coin().run {
                    defaultX = x*BASE_WIDTH
                    position(defaultX, y * BASE_HEIGHT+ OFFSET)
                }
            }

            ITEM_TYPE.OBSTACLE -> {
                Obstacle().run {
                    defaultX = x*BASE_WIDTH
                    position(defaultX, y * BASE_HEIGHT+ OFFSET)
                }
            }

            ITEM_TYPE.ENEMY -> {
                Enemy().run {
                    defaultX = x*BASE_WIDTH
                    position(defaultX, y * BASE_HEIGHT+ OFFSET)
                }
            }
            else -> {
                null
            }
        }

        items.add(item)

    }
}
```
然後在GamePlay用addChild()把每個物件加入場景，並隨後開始載入圖片
```
items.forEach {
    it?.also {
        parentView.addChild(it)
        it.load()
    }
}
```
試著跑程式看看， 我們把這些遊戲物件都放在上頭囉！
![https://yayachang.github.io/ithome2020/images/day15-02.png](https://yayachang.github.io/ithome2020/images/day15-02.png)

### 總結
原始碼我會在最後Day30貼上GitHub位置(筆者是邊寫文章邊開發，程式碼還是有可能會變動)，所以不用太擔心我貼的只有程式碼的片段，主要還是大家要試著用自己的想法邏輯去練習寫出來，才會變成自己的東西唷！ 下一回就要介紹外星人角色登場囉！
