# [Day27] 排行榜畫面加入API
經歷了伺服器端的開發跟上一回的串接API，都是在寫程式默默地在背後做事，是不是有點想念生動的遊戲畫面了呢？ 這一回就要把最最最後的排行榜畫面整合API，真的要把排行榜呈現在各位眼前了。

### 先加入GSON套件
[前一篇](https://yayachang.github.io/ithome2020/day26)呼叫排行榜API回傳的內容如下：
```
[{
	"id": 1,
	"user": "yaya",
	"score": 7
}, {
	"id": 2,
	"user": "korge",
	"score": 6
}, {
	"id": 3,
	"user": "kotlin",
	"score": 5
}]
```
筆者本來要試KorGE內建提供的Json功能，寫了以下的Code:
```val resultList = Json.parseTyped<List<UserScore>>(jsonResult, Mapper)```
但發現怎麼轉換都沒辦法把回傳的Json String轉型成為List<UserScore>。
反而出現這個錯誤，```java.lang.ClassCastException: java.util.LinkedHashMap cannot be cast to model.UserScore```。
只好自己額外去gradle加入GSON幫忙轉型，因此要先在build.grale.kts這個檔案多加上gson，然後gradle sync。
```
dependencies {
   add("commonMainApi", "com.google.code.gson:gson:2.8.6")
}
```
Sync完後寫上程式，一直以為沒加成功，因為IDE一直給我紅字錯誤沒辦法reference，可是實際編譯執行時是會成功的，真是折騰了超久 @@！
![https://yayachang.github.io/ithome2020/images/day27-01.png](https://yayachang.github.io/ithome2020/images/day27-01.png)

### 在Rank.kt加入上傳跟下載排行榜的API**
現在只要在Rank.kt加上程式碼組合就能完成。

先上傳自己的最高分數：
```
api.UserScore.upload(UserScore(user = "yaya", score = getHighestScore()))
```
接著接收排行榜API回傳值：
```
val jsonResult = api.UserScore.getRankList()
val resultList = Gson().fromJson<List<UserScore>>(jsonResult, object : TypeToken<List<UserScore>>() {}.type)
```
開始讀取排行榜回傳轉型好的List<UserScore>資料，並把排名、名稱跟分數畫在Rank畫面上：
```
var upperObject = myScoreImage//在上頭的物件，我的最高分數UI
var index = 1
for(data in resultList){
    val nameString = "$index: ${data.user}" //排名、名稱字串
    val nameBitamp = NativeImage(width = width.toInt(), height = 100).apply {
        getContext2d().fillText(nameString,
                x = 0,
                y = fontSize,
                font = ttfFont,
                fontSize = fontSize.toDouble(),
                color = Colors.BLACK)
    }
    val rankResultImage = image(nameBitamp) {
        position((image.scaledWidth) / 2 - ((myScoreString.count() * fontSize) / 4 + score.width/2), 20)
    }


    rankResultImage.alignTopToBottomOf(upperObject)//放在上頭的物件之下
    upperObject = rankResultImage

    //加入分數
    val score = Score().apply { load() }
    addChild(score)
    score.initPosition()
    score.nowValue = data.score
    score.update()

    score.alignTopToTopOf(rankResultImage)
    score.y -= (score.scaledHeight/2  + 10.0)

    index ++//名次加1

}
```
最後的排行榜畫面就出爐了！(有點醜醜的工程版….)

![https://yayachang.github.io/ithome2020/images/day27-02.png](https://yayachang.github.io/ithome2020/images/day27-02.png)

### 總結
排行榜部分還有很多地方可以做改進，例如目前顯示三個名次就已經是畫面的極限，要顯示更多的名次的話，可能就要用ScrollBar的元件，想試試看的可以參考有人做好的UI元件：https://github.com/korlibs/korge-samples/tree/master/ui 。

還有眼尖的人可能會發現怎麼沒有讓人輸入名字的地方，當然是筆者沒有去做啦，目前想到的解法是問問看開發團隊有沒有支援像是手機開發都有的EditText或是Inputfield元件，或是你可以土法煉鋼的方式去把所有數字跟英文字用Keyboard偵測的方式來做，然後做一個虛擬鍵盤，(就留給各位練習囉)。

到了第27天，算是把外星人跑酷的小遊戲的雛形做完了!  想要做得更完整更好玩的部分就留給各位讀者發揮，相信大家一定會做出非常好玩的遊戲！

最後可以再回頭看看[Day02的遊戲架構](https://yayachang.github.io/ithome2020/day02)，我們確實一步一腳印把整個架構練習完畢了。! 給自己拍拍手吧！

![https://yayachang.github.io/ithome2020/images/day27-03.png](https://yayachang.github.io/ithome2020/images/day27-03.png)
