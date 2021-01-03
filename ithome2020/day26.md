# [Day26] Client端串接API
伺服器端已經完成使命，把API跟資料庫都串接好了，接下來就換KorGE的Client端部分來串接API跟伺服器端整合囉！

### 準備分數資料上傳
我們在Client端部分也是需要用UserScore類別來表示玩家的分數，所以也在KorGE的專案建立src/commonMain/model的資料夾，建立新的檔案UserScore.kt。

![https://yayachang.github.io/ithome2020/images/day26-01.png](https://yayachang.github.io/ithome2020/images/day26-01.png)

```
data class UserScore(var id: Long? = null, var user: String, var score: Int) {
    companion object{
        init {
            Mapper.registerType(UserScore::class) {
                UserScore(it["id"].gen(), it["user"].gen(), it["score"].gen())
            }
            Mapper.registerUntype(UserScore::class) {
                mapOf(
                        "id" to it.id,
                        "user" to it.user,
                        "score" to it.score
                )
            }
        }
    }
}
```
這邊的初始化部分我們加了一個```Mapper.registerType```跟```Mapper.registerUntype```是為了能直接將UserScore物件資料轉換成Json格式的字串而做的準備，否則無法正常傳入伺服器端處理。

### 分數上傳API
我們在建立另一個資料夾api，專門來放負責呼叫api部分的程式，因此在KoreGE專案建立src/commonMain/api的資料夾，繼續建立新的檔案UserScore.kt。

![https://yayachang.github.io/ithome2020/images/day26-02.png](https://yayachang.github.io/ithome2020/images/day26-02.png)

```
object UserScore {

    suspend fun upload(data: UserScore){//分數上傳API        
    }
    suspend fun getRankList(){//取得排行榜API
        
    }
}
```
接著開始寫呼叫上傳分數的方法 ```fun upload(data:UserScore)```，這裡使用了```HttpClient().requestAsString```的方法，然後呼叫的類型是POST，位置是url = "http://0.0.0.0:8080/uploadScore" ，傳入的內容cotent就是 ```Json.stringifyTyped(data, Mapper)```，前面說的要把玩家分數物件轉為Json字串的部分。
```
suspend fun upload(data: UserScore){
    val content = Json.stringifyTyped(data, Mapper)
    val result = HttpClient().requestAsString(method = Http.Method.POST, url = "http://0.0.0.0:8080/uploadScore", content = content.openAsync())
    if(result.success){
        println("更新分數成功")
    }
}
```
如果Htpp request成功，收到伺服器回傳，就在console印出更新分數成功。

### 取得排行榜API
取得排行榜的方法 ```fun getRankList()```相對上傳分數簡單，因為不用特別要帶入UserScore的資料，只要單純呼叫就好。 所以一樣使用```HttpClient().requestAsString```的方法，然後呼叫的類型是GET，位置是url = "http://0.0.0.0:8080/getRankList"。
```
suspend fun getRankList(){
    val result = HttpClient().requestAsString(method =Http.Method.GET, url = "http://0.0.0.0:8080/getRankList")
    if(result.success){
        println("取得排行榜成功 data:${result.content}")
    }
}
```
如果Htpp request成功，收到伺服器回傳，就在console印出取得排行榜成功，而且排行榜是會回傳排行榜資料，所以還會把回傳內容印出。

### 測試呼叫API
我們可以先把呼叫API的部分先放Rank.kt的Container.sceneInit()裡頭或是任何程式會跑過的地方，先測試看看console會不會印成功。
```
launch {
    api.UserScore.upload(UserScore(user = "yaya", score = 123))
    api.UserScore.getRankList()
}
```
執行結果可以看到uploadScore跟getRankList都回應成功，表示在KorGE呼叫Ktor的API成功啦!!
![https://yayachang.github.io/ithome2020/images/day26-03.png](https://yayachang.github.io/ithome2020/images/day26-03.png)

### 總結
遊戲的資料從玩家在玩的本機端(Client端)上傳，以及由伺服器端接收API的呼叫跟資料庫處理產生排行榜的一連串動作，已經從都講解練習完囉，排行榜最後的設計就將在下一篇結束了！
