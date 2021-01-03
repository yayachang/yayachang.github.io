# [Day24] 串接遊戲資料庫-使用Exposed
前兩篇都在為今天這篇來鋪成的，因為已經學會建立Ktor專案，也把MySQL資料庫建立好，現在就要準備來做資料庫程式設計的部分囉。

### 設計排行榜資料庫
因為其實我們只是一個小遊戲的排行榜，所以資料庫的內容設計就相對來說比較簡單，只要一個資料表就能達成我們線上排行榜的需求了。

首先我們先拿示意做個表格出來，有該筆紀錄的流水號，玩家名稱跟最高分數，還有更新分數的時間。

![https://yayachang.github.io/ithome2020/images/day24-01.png](https://yayachang.github.io/ithome2020/images/day24-01.png)

有這張表基本上就能用[上一篇](https://yayachang.github.io/ithome2020/day23)所學用MySQL Workbench手動見這張表格就行啦！(是這樣說沒錯啦)。
不過我們不用特別去開MySQL Workbench去建立資料表，我們只要在Ktor裡使用Exposed就能幫我們達到設計資料庫這件事了！

### 什麼是Exposed呢？
Exposed是一種ORM資料庫的框架，支援的資料庫類型也很多，一般常用的MySQL，PostgreSQL都有支援，也正好筆者教大家安裝的也是MySQL。想要看更詳細的介紹的讀者可以去官網閱讀，筆者就主要帶大家實際操作來做出我們的遊戲排行榜。

[官網介紹連結: https://github.com/JetBrains/Exposed](https://github.com/JetBrains/Exposed)

Exposed提供用兩種方式來存取資料庫，一種用SQL DSL去寫，另一種就是這次練功使用的DAO方式來寫，筆者比較習慣使用DAO的方式去建立所以大家就看用DAO做的方法囉。

### 安裝Exposed套件
先到[Day22](https://yayachang.github.io/ithome2020/day22)建立好的Ktor專案裡的build.gradle的檔案裡加入以下三行的dependencies:
```
implementation "org.jetbrains.exposed:exposed-core:0.27.1"
implementation "org.jetbrains.exposed:exposed-dao:0.27.1"
implementation "org.jetbrains.exposed:exposed-jdbc:0.27.1"
```

### 建立Table
再來我們新建一個entity資料夾，裡面專門放我們建表的程式檔案，新建一個UserScores.kt的檔案。(把user當作唯一的所以要多加.uniqueIndex()，而分數預設都是0需要設成.default(0))。
```
object UserScores : org.jetbrains.exposed.dao.id.LongIdTable() {
    var user = varchar("user", 255).uniqueIndex()
    var score =  integer("score").default(0)
    var updateTime = long("updateTime")
}
```

### 建立Model
再新建一個model資料夾，專門放建立存取資料庫的類別，我們再新建一個檔案UserScore.kt。
```
data class UserScore(var id:Long, var user:String, var score:Int)
```

### 建立Repository
接著再建立一個repository的資料夾，Repository會負責進行資料庫的CURD動作，我們建立一個新檔案UserScoreRespository.kt，然後會有fun add(), fun update(), fun get()這三個methods，分別代表了排行榜資料的新增、更新、取得。
```
class UserScoreRepository {
    suspend fun add(data: UserScore) {
        transaction {
            UserScores.insert {
                it[user] = data.user
                it[score] = data.score
                it[updateTime] = System.currentTimeMillis()
            }
        }
    }

    suspend fun update(data: UserScore) {
        transaction {
            UserScores.update({ (UserScores.user eq data.user) and (UserScores.score less data.score) }) {
                it[score] = data.score
                it[updateTime] = System.currentTimeMillis()
            }
        }
    }


    suspend fun get(): List<UserScore> {
        val data = withContext(Dispatchers.IO) {
            transaction {
                UserScores.selectAll().orderBy(UserScores.score, SortOrder.DESC).mapNotNull {
                    toRank(it)
                }.toMutableList()
            }
        }
        return data
    }

    private fun toUserScore(row: ResultRow): Rank =
        Rank(
            id = row[UserScores.id].value,
            user = row[UserScores.user],
            score = row[UserScores.score]
        )

}
```
其中特別提出來說的是更新時，```UserScores.update({ (UserScores.user eq data.user) and (UserScores.score less data.score) })```我們會進行比對資料庫的user欄位跟傳入的user資料是否相同，以及資料庫的分數是否比傳進來新的資料分數還要小，若符合此條件才會真正更新分數。

### 資料庫建立及連線設定
資料庫的建立跟連線需要安裝兩個套件， 連接MySQL的JDBC Driver跟HikariCP。
```
implementation 'mysql:mysql-connector-java:8.0.19'
implementation "com.zaxxer:HikariCP:3.4.5"
```

我們再建立一個資料夾database，就把資料庫的初始跟設定連接放在這裡，然後建立一個DatabaseFactory，裡頭就是連接我們上一篇建好的MySQL server位置。接著就連建立新的排行榜Table Ranks。
```
object DatabaseFactory {
    fun init() {
        Database.connect(hikari())
    }
    
    fun createTable(){
        transaction {
            SchemaUtils.create(UserScores)
        }
    }
    
    private fun hikari(): HikariDataSource {
        val config = HikariConfig()
        config.driverClassName = "com.mysql.cj.jdbc.Driver"
        config.jdbcUrl = "jdbc:mysql://localhost:3306/mygame"
        config.username = "xxxxx"
        config.password = "xxxxx"
        config.validate()
        return HikariDataSource(config)
    }

}
```

此處的HikariConfig就是要填寫你的MySQL Server的位置跟帳號密碼囉。
```
config.driverClassName = "com.mysql.cj.jdbc.Driver"
config.jdbcUrl = "jdbc:mysql://localhost:3306/mygame"
config.username = "xxxxx"
config.password = "xxxxx"
```
以上全部都準備好後，在IntelliJ的專案結構會長這樣：

![https://yayachang.github.io/ithome2020/images/day24-02.png](https://yayachang.github.io/ithome2020/images/day24-02.png)

### 實際連線
前面的是設定都準備好的話，就可以在每次Ktor程式的進入點呼叫資料庫的 ```初始化DatabaseFactory.init()跟建立Table的DatabaseFactory.createTable()```，不用擔心已經建立過的Table又再建立會出現問題，這裡如果有建立過的Table再呼叫會略過保留原先的Table。
```
fun Application.module(testing: Boolean = false) {

    DatabaseFactory.init()
    DatabaseFactory.createTable()
}
```

用gradle run將Ktor程式執行起來，接著開啟你的MySQL Workbench來看看，是不是UserScores的Table跟欄位就被建立起來了!!

![https://yayachang.github.io/ithome2020/images/day24-03.png](https://yayachang.github.io/ithome2020/images/day24-03.png)

雖然還沒塞資料進去，但是如果你下查詢指令，可以看到欄位的名稱。

![https://yayachang.github.io/ithome2020/images/day24-04.png](https://yayachang.github.io/ithome2020/images/day24-04.png)

### 新增、更新、查看排行榜資料
已經確定能建立Ranks的資料表了，已經可以呼叫新增跟更新還有查看排行榜的功能，以下就先寫個簡單的測試程式來驗證結果。
```
val repository = UserScoreRepository()
repository.add(UserScore(user="yaya", score=77))

val insertResult = repository.get()
for(data in insertResult){
    System.out.println("new data:${data}")
}
repository.update(UserScore(user="yaya", score=88))

val updateResult = repository.get()
for(data in updateResult){
    System.out.println("update data:${data}")
}
```
你可以到IDE的Run視窗，看到新增跟更新的log。
```
new data:UserScore(id=1, user=yaya, score=77)
update data:UserScore(id=1, user=yaya, score=88)
```
用MySQL Workbench的結果看確實最後的結果是記到yaya得到88分

![https://yayachang.github.io/ithome2020/images/day24-05.png](https://yayachang.github.io/ithome2020/images/day24-05.png)

### 總結
現在已經學會怎麼在Ktor使用Exposed建立資料庫以及連線資料庫，然後對資料庫進行存取了，我們也把排行榜實際要建立、更新、查詢的功能做出來，接下來就是要介紹怎麼在Ktor後端寫出API讓我們的KorGE能呼叫進行連線囉！

### 參考文章
* [https://github.com/JetBrains/Exposed](https://github.com/JetBrains/Exposed)
