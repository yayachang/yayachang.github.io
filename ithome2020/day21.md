# [Day21] Rank排行榜設計-本機存檔使用VfsFile
玩完遊戲後的分數就要記下來送入排行榜來比高下了，但是事情好像用一句話就講完，可是好像要設計的東西可能不比設計遊戲核心少喔。 

### 本機計分
首先要記下玩家在該回合所得到的分數，接著要跟玩家的最高歷史紀錄做比較，如果玩家的分數有比歷史紀錄高，就要先變更最高歷史紀錄。

### 使用VfsFile記下最高分數
咦？好像都沒看過什麼```VfsFile```這個東西，其實早在前面好幾篇都使用過囉！
在讀取圖片素材時，我們使用的 ```resourcesVfs["xxxxx.png"].readBitmap()```方法，而 ```resourcesVfs["xxxxx.png"]```就是```VfsFile```，就是KorGE內建的寫檔讀檔的類別。
記下最高分數就只需要一個數字，不過寫檔的話我們就會需要一個檔案，取名為score.txt。
```
val scoreFile = resourcesVfs["score.txt"]
```
接著程式會檢查score.txt檔案是否存在```src/commonMain/kotlin/resources```的資料夾底下，如果不存在就會進行建立新檔案的動作
```
if(!scoreFile.exists()){
    scoreFile.open(VfsOpenMode.CREATE_NEW)
}
```
計分檔案存在的話，就開始讀取檔案的資料，初始分數先設為0，如果讀檔有資料，將分數字串轉成Int。
```
val savedScoreString = scoreFile.readString()
    var savedScore = 0
    if(savedScoreString.isNotEmpty()){
        savedScore = savedScoreString.toInt()
    }
```    
最後跟存在遊戲回合結束記下的SharedData的分數進行比大小，如果當次分數比儲存的分數高，就會把最高分數寫進score.txt
```
if(SharedData.GAME_SCORE > savedScore){
        savedScore = SharedData.GAME_SCORE
        scoreFile.writeString(SharedData.GAME_SCORE.toString())
        println("new savedScore = $savedScore")
}
```
講上面的程式碼串起來，放到Rank.kt裡，整個本機端計分```getHighestScore()```方法就完成了。
```
suspend fun getHighestScore():Int{
    val scoreFile = resourcesVfs["score.txt"]
    if(!scoreFile.exists()){
        scoreFile.open(VfsOpenMode.CREATE_NEW)
    }
    val savedScoreString = scoreFile.readString()
    var savedScore = 0
    if(savedScoreString.isNotEmpty()){
        savedScore = savedScoreString.toInt()
    }
    if(SharedData.GAME_SCORE > savedScore){
        savedScore = SharedData.GAME_SCORE
        scoreFile.writeString(SharedData.GAME_SCORE.toString())
    }
    return saveScore
}
```
### 顯示自己的最高分數在畫面上
有了玩家自己的最高分數後，我們才會將紀錄上傳至伺服器讓伺服器的程式幫助我們跟全部線上的玩家進行排行榜的比分。
![https://yayachang.github.io/ithome2020/images/day21-01.png](https://yayachang.github.io/ithome2020/images/day21-01.png)

### 總結
這次就算是在設計排行榜本地端儲存分數的方式，為了接續的的篇章做鋪路，到結束之前將會教大家使用最近還滿熱門的Kotlin後端的框架-Ktor，KorGE作者自己設計的遊戲也是使用Ktor來建立他的遊戲伺服器，剛好筆者最近也有學習Ktor，所以就拿Ktor幫助我們完成剩下的排行榜系統的設計囉！

### 參考文章：
* [https://korlibs.soywiz.com/korio/#vfsfile](https://korlibs.soywiz.com/korio/#vfsfile)
