# [Day09] 使用SpriteAnimations

已經連兩篇都介紹跟動畫特效有關的文章了，但是還不夠唷，這一回正要介紹遊戲製作也很常用的Sprite Animations。

### Sprite Animations
```SpriteAnimation```物件就是由一連串連續的圖片組合而成的，遊戲中的角色或是物品會有連續動作想要呈現時，我們就能先畫好這些圖片影格，一張一張照順序排好，接著讓```SpriteAnimaiton```幫我們播放出來。而這些一張一張的影格我們會稱作```Sprite Sheet```，如果你手邊有這些動畫影格，就可以將它合併成一組png檔案，提供給KorGE來讀檔使用。

### 準備圖片
上網搜尋應該都有很多圖源，不過想要商用或是公開出來的還是要注意是否需要授權或是付費唷，這邊筆者找到一個免費的資源，通常我們還是要在作品裡特別感謝他們提供這些寶貴的資源。選這套的原因是單存是因為裡頭的外星人角色動作很可愛，也有一些地板上的素材跟障礙物素材，很適合拿來做跑酷類型遊戲。

圖片來源: [https://kenney.nl/assets/platformer-art-deluxe](https://kenney.nl/assets/platformer-art-deluxe)

![https://yayachang.github.io/ithome2020/images/day09-01.png](https://yayachang.github.io/ithome2020/images/day09-01.png)

下載完檔案後，解壓縮圖檔資料夾可以找到綠色外星人每一張走路的圖片
platformer-art-complete-pack-0 /Base pack /Player/p1_walk/PNG/p1_walk01～p1_walk11.png

![https://yayachang.github.io/ithome2020/images/day09-02.png](https://yayachang.github.io/ithome2020/images/day09-02.png)

### 轉換成Sprite Sheet
我們要先把它先轉換成一張Sprite Sheet為green_alien_walk.png，這時可以利用TexturePacker這個工具幫助你轉換。
工具來源：[https://www.codeandweb.com/texturepacker](https://www.codeandweb.com/texturepacker)

安裝好後，將那11張圖片拖曳到中間區域

![https://yayachang.github.io/ithome2020/images/day09-03.png](https://yayachang.github.io/ithome2020/images/day09-03.png)

然後右邊可以看到Packing區塊，Alogrithm選Grid/Strip(因為這樣讀取每張圖片時才不會大小不一樣), Max size選1024。

![https://yayachang.github.io/ithome2020/images/day09-04.png](https://yayachang.github.io/ithome2020/images/day09-04.png)

接著再點選上方的Publish sprite sheet按鈕。

![https://yayachang.github.io/ithome2020/images/day09-05.png](https://yayachang.github.io/ithome2020/images/day09-05.png)

命名green_alien_walk.png放到專案資料夾的/src/commonMain/kotlin/resources裡頭

![https://yayachang.github.io/ithome2020/images/day09-06.png](https://yayachang.github.io/ithome2020/images/day09-06.png)

### 開始寫程式
因為外星人走路我們預計會是在GamePlay畫面遊戲開始時才會出現，所以我們就先打開先前有建立好的GamePlay.kt檔案，把讀取SpriteSheet跟播放SpriteAnimation的程式寫到Container.ScenceMain()裡頭。
```
val spriteMap = resourcesVfs["green_alien_walk.png"].readBitmap()
val alienWalkAnimation = SpriteAnimation(
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
val alien = sprite(alienWalkAnimation)
alien.spriteDisplayTime = 0.1.seconds
alien.playAnimationLooped()
```
讀取檔案的方式大家應該都不陌生了，主要是SpriteAnimation要設定的內容有spriteMap，也就是我們做好的外星人Sprite Sheet檔案，然後我們要給播放圖片的寬度spriteWidth=72跟高度spriteHeight=97(請看每張的外星人圖片檔案都是這個大小)，然後有欄位columns=11，列是rows=1。
```
SpriteAnimation(
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
```
最後將這SpriteAnimation傳給sprite物件，然後播放的頻率設為0.1秒，並設成無限制播放。
```
val alien = sprite(alienWalkAnimation)
alien.spriteDisplayTime = 0.1.seconds
alien.playAnimationLooped()
```
執行程式後就能看到綠色外星人在原地走動了！

![](https://yayachang.github.io/ithome2020/images/day09-07.gif)

想要讓他移動就加上上回學過的tween囉！
```
while (true) {
    alien.tween(alien::x[0.0, 512.0], time = 3000.milliseconds)
    delay(1.seconds)
}
```
果然外星人開始移動囉！！

![](https://yayachang.github.io/ithome2020/images/day09-08.gif)

### 總結
學到這邊應該有覺得越來越好玩了，因為有角色開始活了起來，能開始走動了！之後SpriteAnimation會應用到很多需要動起來的遊戲物件上，所以除了綠色外星人之外，其他的外星人也能當作練習的材料，你也開始動手做吧！

### 參考文章
* [https://korlibs.soywiz.com/korge/reference/sprites/](https://korlibs.soywiz.com/korge/reference/sprites/)
* [https://www.youtube.com/watch?v=fY7a2xrHL9g](https://www.youtube.com/watch?v=fY7a2xrHL9g)
