# [Day25] 設計API-上傳分數及下載排行榜

排行榜資料庫準備好了，可以開始來設計Client端，也就是玩家需要的上傳分數跟取得排行榜的功能。

### 上傳分數的使用情境
玩家yaya玩完結束一回合遊戲後，進行分數上傳分數88分，伺服器收到後，將會先拿yaya的玩家名稱到遊戲資料庫查找是否已經建立過了遊戲分數，如果還沒建立，就會先新增一筆yaya的遊戲分數，若建立過了就會比對更新儲存的資料，決定是否更新該筆紀錄。

### 上傳分數API設計
把上傳分數的邏輯判斷的部分放至所謂Service層，負責接受上層的AP呼叫的資料，把資料進行處理之後再跟Repository層連結。因此建立一個Service資料夾，再新建一個UserService.kt的檔案。

![https://yayachang.github.io/ithome2020/images/day25-01.png](https://yayachang.github.io/ithome2020/images/day25-01.png)

寫一個fun saveScore先判斷玩家user是否有存在，不存在就新增玩家資料，存在的話就更新玩家資料。
```
class UserScoreService {
    
    val userScoreRepository = UserScoreRepository()
    suspend fun saveScore(data: UserScore) {
        if(userScoreRepository.get(data.user) == null){
            userScoreRepository.add(data)
        }else{
            userScoreRepository.update(data)
        }
    }
}
```

上層的api我們把名稱取名為uploadScore，然後建立一個api資料夾，把所有的api設計都放此，接著建立一個新的檔案UserScore.kt。

![https://yayachang.github.io/ithome2020/images/day25-02.png](https://yayachang.github.io/ithome2020/images/day25-02.png)

```
fun Route.userScore() {
    post("/uploadScore") {
        var data = Gson().fromJson(call.receive<String>(), UserScore::class.java)
        if (data == null) {
            call.respond(HttpStatusCode.BadRequest, "玩家資料不存在")
        } else {
            UserScoreService().saveScore(data)
            call.respond(HttpStatusCode.OK)
        }
    }
}
```
在UserScore.kt接收到uploadScore呼叫後，會先判斷上傳的資料是否是空資料，是空資料的話會回傳"錯誤的玩家資料"，若有資料的話則會進行更新分數的流程，若執行正確則會回傳OK給Client端。

### 取得排行榜的使用情境
玩家yaya玩完結束一回合遊戲後，上傳分數成功，進到排行榜畫面，會跟伺服器詢問目前的玩家排名，會在畫面上顯示當下全部玩家的排名跟分數。

### 取得排行榜API設計
把取得排行榜的程式邏輯一樣寫在UserScoreService.kt裡，只要呼叫一行userScoreRepository.get()就能把上一篇在Repository寫好的取得遊戲資料庫全玩家的資料讀出來。
```
class UserScoreService {
    val userScoreRepository = UserScoreRepository()
    
    suspend fun saveScore(data: UserScore) {...}//上傳分數

    suspend fun getRankList():List<UserScore> {//取得排行榜
        return userScoreRepository.get()
    }
}
```
上層的api我們把名稱取名為getRankList，一樣是寫在api/UserScore.kt裡面。
```
fun Route.userScore() {
    post("/uploadScore") {...}//上傳分數API

    get("/getRankList") {//取得排行榜API
        call.respond(HttpStatusCode.OK, UserScoreService().getRankList())
    }
}
```
上面寫好的UserScoreService().getRankList()，回傳List<UserScore>的陣列，就完成這隻API的功能了。

### 測試API
寫好了API當然要簡單測試一下囉，先確認這些功能是能執行無誤，就用最快的手動呼叫API來達成。
記得先呼叫gradle run讓自己的本機端的伺服器服務跑起來。

### 測試取得排行榜API
測試GET的部分比較容易，用網頁就能測出來。

![https://yayachang.github.io/ithome2020/images/day25-03.png](https://yayachang.github.io/ithome2020/images/day25-03.png)

### 測試上傳分數API
測試POST的話，沒辦法直接用網頁呼叫，所以還是推薦各位去下載POSTMAN來呼叫API了。
下載位置：https://www.postman.com/

填好API位置跟上傳需要的user跟score資料（選擇Body, raw, 類型要選JSON(application/json)，按下Send後，就能收到200 OK的回傳了。

![https://yayachang.github.io/ithome2020/images/day25-04.png](https://yayachang.github.io/ithome2020/images/day25-04.png)

當然GET也能用POSTMAN呼叫，試著驗證剛剛的POST呼叫的結果，果然就能收到yaya本來是88分變成100分了，表示更新分數成功囉！

![https://yayachang.github.io/ithome2020/images/day25-05.png](https://yayachang.github.io/ithome2020/images/day25-05.png)

### 總結
終於把伺服器端的遊戲資料庫存取跟給玩家的客戶端需要的API都設計好了！ 離整個遊戲設計完成的距離不遠了，接下來我們只要在KorGE裡把設計好的這兩隻API串接起來，剩下的遊戲排行榜畫面就要完成囉！期待下一篇在KorGE串接API介紹了。
