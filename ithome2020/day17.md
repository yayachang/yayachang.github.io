# [Day17] GamePlay設計-UI介面(分數、血條、計時)
[上一回](https://yayachang.github.io/ithome2020/day17)我們已經幾乎算是可以開始操控外星人來玩遊戲了，但是還是有一些細節還沒有做完，像是看外星人的頭上有一大片的空間，(可以回頭看[Day14](https://yayachang.github.io/ithome2020/day14)我們遊戲關卡介紹)。

![https://yayachang.github.io/ithome2020/images/day17-01.png](https://yayachang.github.io/ithome2020/images/day17-01.png)

我們就利用這(14x2)的來放置遊戲時的狀態跟資訊，像是角色獲得的分數跟目前的血條還有一些額外的想提供給玩家的資訊。

### 遊戲規則
我們分數的規則先簡單的設定：
(1)吃到金幣就得到1分
(2)外星人有5滴血。碰到障礙物會扣1滴血，碰到敵人扣2滴血。
(3)一回合的時間限制是1分鐘，所以會有秒數的倒數計時。
(4)外星人扣完5滴血跟時間到了就結束遊戲。

### 血條UI
清楚規則後，我們就能開始來設計囉，首先我們先從外星人的血條著手吧。

### Blood.kt程式碼：
血條也是要加在GamePlay的Scene，所以也是繼承Container，然後defaultX, defaultY是設定血條的座標位置，count=5表示有五個單位的血包。
會有一個```hearts = arrayListOf<Image>()的Image```陣列來存放表示血量的圖片，有被扣血的imageHeartEmpty跟滿寫的imageHeartFull的Bitamp。
因為規則是5滴血，所最大值maxValue=5，而一開始遊戲的外星人血量初始值initValue=5，nowValue當然也是5囉。
```
class Blood : Container() {

    val count = 5
    val defaultX = ItemManager.BASE_WIDTH
    val defaultY = ItemManager.BASE_HEIGHT / 2
    var hearts = arrayListOf<Image>()
    lateinit var imageHeartEmpty: Bitmap
    lateinit var imageHeartFull: Bitmap

    val maxValue = 5
    val initValue = 5
    var nowValue = initValue

    suspend fun load() {}
    fun initPosition() {}
    fun plus(){}
    fun minus(){}
    fun full(){}
    fun empty(){}
    fun update(){}
}
```
Blood.kt的load動作就是在載入所需圖片，預設都給滿血圖片。
```
suspend fun load() {
    imageHeartEmpty = resourcesVfs["hud_heartEmpty.png"].readBitmap()
    imageHeartFull = resourcesVfs["hud_heartFull.png"].readBitmap()
    for (i in 0 until count) {
        val heart = image(imageHeartFull)
        hearts.add(heart)
    }
}
```
然後要把血條放在遊戲畫面上方的位置，我們在hearts[0]第一張放在預設的defaultX, defaultY，剩下的就是參考前一張的位置用```alignLeftToRightOf跟alignTopToTopOf```對準前一張的Top跟接續著前一張的Right就可以了。
```
fun initPosition() {
    hearts.forEachIndexed { index, image ->
        image.apply {
            if (index == 0) {
                position(defaultX, defaultY)
            } else {
                alignLeftToRightOf(hearts[index - 1])
                alignTopToTopOf(hearts[index - 1])
            }
        }
    }
}
```
血條的增加、減少、重置，當外星人吃到血包或是碰到障礙物或是吃到滿血血包，或是掉到洞裡或碰到必死物件直接歸0。這些狀態就會呼叫```plus()、minus()、empty()、full()```。
```
fun plus(){
    if(nowValue < maxValue){
        nowValue ++
    }
}

fun minus(){
    if(nowValue > 0) {
        nowValue--
    }
}

fun empty(){
    nowValue = 0
}

fun full(){
    nowValue = maxValue
}
```
血條的更新檢查之後fun update()也會放在GamePlay的addHrUpdater裡，以便隨時更新血條資訊。
```
fun update() {
    for (i in 0 until count) {
        if (i < nowValue) {
            hearts[i].bitmap = imageHeartFull.slice()
        } else {
            hearts[i].bitmap = imageHeartEmpty.slice()
        }
    }
}
```
試著加到GamePlay執行後就多出五個愛心血包！

![https://yayachang.github.io/ithome2020/images/day17-02.png](https://yayachang.github.io/ithome2020/images/day17-02.png)

### 分數UI
分數的話就要將數字圖片0到9的單張圖片去做Sprite Sheet，這樣程式比較方便去取用。(忘記怎麼做Sprite Sheet可以回頭去看Day09用Texture Packer去做唷)。

![https://yayachang.github.io/ithome2020/images/day17-03.png](https://yayachang.github.io/ithome2020/images/day17-03.png)

### Score.kt程式碼
分數一樣也是要加在GamePlay的Scene，也是繼承Container，defaultX, defaultY是設定分數的座標位置。
count=5表示會有一個scores的Image陣列來存放表示五位數的分數顯示，所以最多分數只會到9999。
預設遊戲的一開始當然就是從0分開始initValue=0，nowValue=0。
```
class Score : Container() {
    val count = 5
    val BASE_WIDTH = 32
    val BASE_HEIGHT = 40

    val defaultX = ItemManager.BASE_WIDTH * 6
    val defaultY = ItemManager.BASE_HEIGHT / 2

    lateinit var scoreHead: Image
    lateinit var scoreBitmapSlice: Bitmap
    var scores = arrayListOf<Image>()
    val maxValue = 9999
    val initValue = 0
    var nowValue = initValue

    suspend fun load() {}

    private fun loadNumber(value: Int): Bitmap {}

    fun initPosition() {}
    fun plus(){}
    fun update() {}
}
```
load動作就是在載入金幣圖案跟數字圖案，然後五位數的圖片都預設給0。
```
suspend fun load() {
    scoreHead = image(resourcesVfs["hud_coins.png"].readBitmap())
    scoreBitmapSlice = resourcesVfs["numbers.png"].readBitmap()
    for (i in 0 until count) {
        scores.add(image(loadNumber(0)))
    }
}
```
loadNumber比較特別，因為numbers.png是0~9的數字，長寬總共320x40，一張圖片單位就是32x40，所以就依據傳入的value1並用這個extract(x, y, width, height)這個方法來切圖得到對應的圖片。
```
private fun loadNumber(value: Int): Bitmap {
    return scoreBitmapSlice.extract(value * BASE_WIDTH, 0, BASE_WIDTH, BASE_HEIGHT)
}
```
fun initPosition()就是放置金幣的圖案跟分數的位置囉，注意scoreHead跟scores的圖案大小不一樣大，scores的圖案比較小，所以在第一張分數圖，會去把位置方在金幣圖案的中間，在用alignLeftToRightOf去放在金幣的右手邊，然後 x = x + BASE_WIDTH / 2位置加一點空間有點空隙不要擠在一起。
```
fun initPosition() {
    scoreHead.position(defaultX, defaultY)

    scores.forEachIndexed { index, image ->
        image.apply {
            if (index == 0) {
                centerOn(scoreHead)
                alignLeftToRightOf(scoreHead)
                x = x + BASE_WIDTH / 2
            } else {
                alignLeftToRightOf(scores[index - 1])
                alignTopToTopOf(scores[index - 1])
            }
        }
    }
}
```
金幣目前只有增加的規則，所以只有plus()來表示吃到金幣加1分。
```
fun plus(){
    if(nowValue < maxValue){
        nowValue ++
    }
}
```
分數的更新fun update()一樣會放在GamePlay的addHrUpdater裡，以便隨時更新得分資訊
```
fun update() {
    scores[0].bitmap = loadNumber((nowValue / 10000)).slice()
    scores[1].bitmap = loadNumber((nowValue % 10000 / 1000)).slice()
    scores[2].bitmap = loadNumber((nowValue % 1000) / 100).slice()
    scores[3].bitmap = loadNumber((nowValue % 100) / 10).slice()
    scores[4].bitmap = loadNumber((nowValue % 10)).slice()
}
```
試著加到GamePlay執行後就多出分數的資訊了！

![https://yayachang.github.io/ithome2020/images/day17-04.png](https://yayachang.github.io/ithome2020/images/day17-04.png)

### 倒數計時UI
終於要介紹到最後一個UI了，其實做法跟分數很像，只是我們目前只限制只有一分鐘，也就是60秒，所以我們的顯示只有兩位數，也會用到numbers.png這個0~9的圖片。

### GameTimer的程式碼
只有兩個位數的圖所以count=2，timers就是放這兩張圖的陣列，timerHead就是代表計時的圖片，初始值initTime=60，因為一開始由60秒開始倒數!
```
class GameTimer : Container() {

    val count = 2
    val BASE_WIDTH = 32
    val BASE_HEIGHT = 40
    val defaultX = ItemManager.BASE_WIDTH * 10
    val defaultY = ItemManager.BASE_HEIGHT / 2

    lateinit var timerHead: Image
    lateinit var timerBitmapSlice: Bitmap
    var timers = arrayListOf<Image>()
    val initTime = 60
    var totalTime = initTime

    suspend fun load() {}
    private fun loadScore(value: Int): Bitmap {}
    fun initPosition() {}    
    fun minus() {}
    fun update() {}
}
```
這次的load()跟loadScore()還有initPosition()跟minus()的寫法跟Score.kt很像，這次交給各位讀者自己練習看看(之後會在第三十天比賽結束尾聲把我的Code放在GitHub上)。

所以這邊就講GameTimer的update()寫了些什麼，其實也就只是把目前時間的十位數跟個位數算出來，最後放到GamePlay的addHrUpdater裡更新。
```
fun update() {
    timers[0].bitmap = loadScore((totalTime / 10)).slice()
    timers[1].bitmap = loadScore((totalTime % 10)).slice()
}
```
### 最後放在一起
再回到GamePlay.kt，把前一次在sceneInit()的內容跟這次的內容整合，把遊戲UI資訊放進去。
```
override suspend fun Container.sceneInit() {

    //加入背景
    background = Background().apply { load() }
    addChild(background)

    //加入血條
    heart = Blood().apply { load() }
    addChild(heart)
    heart.initPosition()
    heart.update()

    //加入分數
    score = Score().apply { load() }
    addChild(score)
    score.initPosition()

    //加入倒數計時
    gameTimer = GameTimer().apply { load() }
    addChild(gameTimer)
    gameTimer.initPosition()

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
然後Container.sceneMain()多加以下的Code:
gameTimer.minus()表示要每秒做倒數，所以必須放在更新固定頻率的addFixedUpdater(1.seconds){}。
```
addFixedUpdater(1.seconds){
    gameTimer.minus()
}
```
addHrUpdater{}就放血條、分數、跟倒數計時的更新就行了
```
addHrUpdater {
    background.move()
    ItemManager.move()
    alien.update()
    blood.update()
    score.update()
    gameTimer.update()
}
```
這回的在遊戲上方的UI資訊的成果就出爐囉！

![](https://cdn-images-1.medium.com/max/640/1*sFj5iXbFVtwaWEH3NZW7iQ.gif)

### 總結
目前只有計時器的數字有在變化，不過沒關係，因為我們下回才會學到外星人吃金幣跟碰到敵人跟障礙物的程式實作，也就是會介紹KorGE的碰撞偵測，請期待下回囉！
