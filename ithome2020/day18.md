# [Day18] GamePlay設計-碰撞偵測

這次終於要介紹這次小遊戲的主要核心部分，就是遊戲中的角色跟場景裡的障礙物還有敵人的互動了！

### 碰撞偵測
在KorGE要實現兩個物體間的碰撞其實非常非常的簡單！因為KorGE在View的物件都有實現```collidesWith```這個方法。只要把要偵測的物件填入，然後就會告訴你是否兩個物件接觸了。
```
fun View.collidesWith(other: View, kind: CollisionKind = CollisionKind.GLOBAL_RECT): Boolean {
    return collisionContext.collidesWith(this, other, kind)
}
```
如果要一次偵測多個物件不用一個一個判斷，也能傳入List<View>。
```
fun View.collidesWith(otherList: List<View>, kind: CollisionKind = CollisionKind.GLOBAL_RECT): Boolean {
    val ctx = collisionContext
   otherList.fastForEach { other ->
      if (ctx.collidesWith(this, other, kind)) return true
   }
   return false
}
```
```collidesWith```這個方法會是以物件的圖片所佔的整張大小(包含有去背的部分)去偵測範圍，但是有時候你的物件可能會有一部分才需要偵測到碰撞，例如可能你的敵人只有拿武器的地方碰到，或是障礙物只有尖刺的地方碰到。
這時你可以另外用collidesWithShape，自己去指定要偵測的範圍，可以看到CollisionKind的參數，從CollisionKind.GLOBAL_RECT換成CollisionKind.SHAPE了。
```
fun View.collidesWithShape(other: View): Boolean = collidesWith(other, CollisionKind.SHAPE)
fun View.collidesWithShape(otherList: List<View>): Boolean = collidesWith(otherList, CollisionKind.SHAPE)
```
不過用```collidesWithShape```的話，你的View就要額外寫hitShape來定義你的偵測範圍囉。所以接下來就用我們的障礙物石頭來示範碰撞偵測的功能。

### Obstacle.kt程式碼
```
class Obstacle : Item() {
    
    override suspend fun load() {
        val bitamp = resourcesVfs["rock.png"].readBitmap()
        solidRect(bitamp.width, bitamp.height, Colors.BLUE).xy(0.0, 0.0)
        image(bitamp)
        hitShape {
            rect(0.0, 0.0, bitamp.width, bitamp.height)
        }
    }
}
```

我們的Obstacle會載入rock.png這張石頭的圖片，如果你有看原始檔案會發現其實這張圖並沒有去背，所以原始大小是70x70。

![https://yayachang.github.io/ithome2020/images/day18-01.png](https://yayachang.github.io/ithome2020/images/day18-01.png)

因此我在fun load的裡頭寫了一個幫助我Debug的solideRect來幫助我了解目前這張圖片實際的會被偵測到的碰撞範圍。
```
solidRect(bitamp.width, bitamp.height, Colors.BLUE).xy(0.0, 0.0)
```

![https://yayachang.github.io/ithome2020/images/day18-02.png](https://yayachang.github.io/ithome2020/images/day18-02.png)

可以發現以這個高度，根本就不是石頭的可見範圍，外星人怎麼樣跳躍基本上都一定會被誤判被撞到，所以這時候就試著調整只有大概只有一半石頭的高度來看看。
```
solidRect(bitamp.width, bitamp.height/2, Colors.BLUE).xy(0.0, bitamp.height/2)
```

![https://yayachang.github.io/ithome2020/images/day18-03.png](https://yayachang.github.io/ithome2020/images/day18-03.png)

當確定藍色的debug偵測範圍確定後，你就能再把hitShape改成你最後決定的偵測範圍。這樣就會是外星人跟障礙物石頭偵測碰撞的範圍了。
```
hitShape {
    rect(0.0, bitamp.height/2, bitamp.width, bitamp.height/2)
}
```
不過通常我會再把石頭的偵測範圍再縮小一些，因為實際在玩的時候，因為其實石頭是不規則的，看藍色debug矩形還是有一些地方其實根本還是在石頭外，外星人在起跳後可能還是會碰到，但是玩家的視覺上根本就沒有碰到，這樣還是會照成誤判，可能比較好的做法是能依據石頭的形狀去偵測(筆者暫時還沒找出這樣的解法@@)，但比較簡單的做法就是縮小偵測矩陣了，所以多加了一些Offset來調整碰撞區域。
```
class Obstacle : Item() {

    val offsetX = 25.0
    val offsetY = 20.0

    override suspend fun load() {
        val bitamp = resourcesVfs["rock.png"].readBitmap()
        image(bitamp)
        solidRect(bitamp.width - offsetX, (bitamp.height + offsetY) / 2, Colors.BLUE).xy(offsetX / 2, (bitamp.height + offsetY) / 2)
        hitShape {
            rect(offsetX / 2, (bitamp.height + offsetY) / 2, bitamp.width - offsetX, (bitamp.height + offsetY) / 2)
        }
    }
}
```
至於另一個要碰撞偵測的就是金幣了，不過金幣基本上沒有像石頭一樣有太多的去背，所以就不用特別要加上hitShape去偵測範圍了。

![https://yayachang.github.io/ithome2020/images/day18-04.png](https://yayachang.github.io/ithome2020/images/day18-04.png)

### 碰撞處理
接下來要處理外星人碰到金幣跟障礙物跟敵人的動作了，外星人碰到金幣理所當然就是金幣的UI要加1，所以會呼叫我們Day17做好的Score.kt的plus()，而如果是碰到障礙物或是敵人，則是要呼叫Blood.kt的minsu()表示被扣血了。

外星人的部分，因為碰撞到了敵人跟障礙物，當然會要有一點痛苦的表情，所以會在Alien.kt裡頭放入受傷來表現。

![https://yayachang.github.io/ithome2020/images/day18-05.png](https://yayachang.github.io/ithome2020/images/day18-05.png)

### Alien.kt程式碼
雖然痛苦的表情只有一張，但是我還是選擇用SpriteAnimation來做。
```
suspend fun load() {
    spriteMap = resourcesVfs["green_alien_walk.png"].readBitmap()
    hurtAnimation = SpriteAnimation(spriteMap = hurtBitmap, spriteWidth = 69, spriteHeight = 92)
}
```
不用單張圖片，而是選擇用SpriteAnimation主要是因為他可以控制播放圖片的秒數，播放完後，我就能再繼續受傷前的狀態，例如受傷前是走路，播完受傷畫面後就回到走路，上一次是跳耀或降落時的狀態就持續下去，看起來會比較自然。實作的程式碼如下:
```
fun hurt(): Boolean {
    if (status == STATUS.HURT) {//若已經是受傷狀態，就不用再做
        return false
    } else {
        changeStatus()
        sprite = sprite(hurtAnimation) {
            spriteDisplayTime = 0.3.seconds//播放0.3秒
        }.apply {
            playAnimation(1)
            onAnimationCompleted {//動畫結束回到前一狀態
                status = lastSTATUS
                if (status == STATUS.WALK) {
                    walk()
                }
            }
        }
        lastSTATUS = status//儲存上次狀態
        status = STATUS.HURT//將狀態設為受傷
        return true
    }
}
```
若受傷時通常也會角色閃爍來表現，所以在update裡頭我們多加了受傷狀態，圖片會從aplha=1減少到0.5再加回1的方式，來做出閃爍效果。
```
fun update() {
    when (status) {
        STATUS.HURT -> {
            if (sprite.alpha > 0) {
                sprite.alpha -= 0.1
            } else if (sprite.alpha <= 0.5) {
                sprite.alpha = 1.0
            }
        }
    }
}
```
### 回到GamePlay.kt
我們已經幫障礙物還有敵人都加了```hitShape```了，(敵人程式碼可參考之後補充的程式碼，或各位能用障礙物的寫法來依此類推)，就剩下幫它們一起加上碰撞偵測```collidesWithShape```，而外星人的碰撞偵測部分就是加上ItemManager.scoreItem(得分物件的List)跟ItemManager.hurtItem(傷害物件的List)
。
```
alien.addUpdater {
    if(collidesWithShape(ItemManager.scoreItem)){
        score.plus(1)//分數加1
    }
    if(collidesWithShape(ItemManager.hurtItem)){
        if(hurt()) {
            blood.minus()//血包減1
        }
    }
}
```
障礙物跟敵人還有金幣都統一在ItemManager.kt控管，所以當外星人在GamePlay.kt產生後，也就順便用setCollision來進行碰撞偵測的處理：
```
fun setCollision(alien: Alien, parentView: Container) {
    items.forEach {
        when (it) {
            is Coin -> {
                it.addUpdater {
                    if (collidesWith(alien.sprite)) {
                        parentView.removeChild(this)
                    }
                }
            }
            is Enemy -> {
                it.addUpdater {
                    if (collidesWithShape(alien.sprite)) {
                        hurtItem.remove(this)
                    }
                }
            }
            is Obstacle -> {

                it.addUpdater {
                    if (collidesWithShape(alien.sprite)) {
                        hurtItem.remove(this)
                    }
                }
            }
        }
    }
}
```
金幣Coin：碰到外星人後其實就是吃掉，就會消失在遊戲畫面上，因此會呼叫```parentView.removeChild(this)```。

Enemy敵人跟Obstacle障礙物: 碰到外星人，但是其實不會消失，只要從ItemManager.hurtItem(傷害物件的List)移除，所以呼叫```hurtItem.remove(this)```就避免一直偵測被扣血包。

### 開始來玩玩!

![https://cdn-images-1.medium.com/max/640/1*FQlX5XOyT8U66mhnnMzASQ.gif](https://cdn-images-1.medium.com/max/640/1*FQlX5XOyT8U66mhnnMzASQ.gif)

外星人已經吃金幣有分數囉！而且金幣也會消失在畫面，碰到怪物敵人也會被扣一個血包，外星人也露出痛苦表情並有閃爍的特效！！ 

### 總結
筆者已經幾乎把所有遊戲的核心GamePlay介紹給大家並實作出來了，剩下的怎麼去編輯關卡的難易度跟關卡的長度都是各位可以試著去動手調整看看的，接下來的篇章會先把開頭的Splash跟Menu內容補齊，還有GameOver跟Rank畫面補齊，這樣差不多就是一個單機的小遊戲囉。

### 參考文章
* [https://korlibs.soywiz.com/korge/reference/physics/](https://korlibs.soywiz.com/korge/reference/physics/)
