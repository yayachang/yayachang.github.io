# [Day22] 建立遊戲伺服器開發環境-安裝Ktor

設計小遊戲的上半場幾乎都是專注在學習KorGE的基本元件使用方法，以及怎麼應用這些元件來將我們的遊戲核心實作出來，而下半場就是要來處理這個線上排行榜系統。

不過KorGE主要是處理遊戲本機端的事情，要製作伺服器當然還是要交給專門的後端框架，也就是接下來介紹的Ktor了！

### Ktor是什麼呢？
簡單來說，Ktor就是一個專門用Kotlin語言打造的後端框架，簡單易懂好上手，我認為非常認為很適合已經學會Kotlin然後又剛入門寫後端程式的人來嘗試。

需要找更詳細的資源可到[官方網站](https://ktor.io/)：https://ktor.io/

### 安裝Ktor
如果已經跟著筆者一直練習開發KorGE的話，一定早就裝好IntelliJ的IDE，而Ktor的開發基本上也是使用IntelliJ的IDE，因此想要自製自己的線上小遊戲非常方便，只要用同一套IDE就前端後端一條龍都能使用！
不過Ktor跟KorGE一樣，都需要先去Plugin下載，這樣開新專案時才會有方便的建立專案的精靈幫你把相關設定都先預備好。
IntelliJ的安裝就不在贅述囉(可以看[Day01](https://yayachang.github.io/ithome2020/day01)的教學)，所以開啟你的IntelliJ的IDE找到Plugin所在處，關鍵字輸入Ktor，立馬進行安裝，重啟後你就能準備使用Ktor囉。

![https://yayachang.github.io/ithome2020/images/day22-01.png](https://yayachang.github.io/ithome2020/images/day22-01.png)

### 建立Ktor專案
安裝好重啟後，在IntelliJ的入口畫面，按下Create New Project開新的專案，可以看到左邊就有Ktor選項。

![https://yayachang.github.io/ithome2020/images/day22-02.png](https://yayachang.github.io/ithome2020/images/day22-02.png)

我們先在Server的Templating勾選HTML DSL 跟Content Negotiation勾選GSON，Client的HttpClient Engine勾選HttpClient Engine跟Apache HttpClient Engine。

![https://yayachang.github.io/ithome2020/images/day22-03.png](https://yayachang.github.io/ithome2020/images/day22-03.png)

下一步就是將伺服器專案取名為myServer，這裡當然就是可以自行取想要的專案名稱囉。

![https://yayachang.github.io/ithome2020/images/day22-04.png](https://yayachang.github.io/ithome2020/images/day22-04.png)

按下一步選擇專案位置，按下Finish後專案建立就大功告成！

![https://yayachang.github.io/ithome2020/images/day22-05.png](https://yayachang.github.io/ithome2020/images/day22-05.png)

接下來我們去專案資料夾找到放程式進入點，也就是src/Application.kt這個檔案。

![https://yayachang.github.io/ithome2020/images/day22-06.png](https://yayachang.github.io/ithome2020/images/day22-06.png)

### Application.kt
這些程式碼內容就是建立專案時我們勾選的選項對應產生出來的，可以看見Apache跟Html有關的部分。 不過Html不是我們這次小遊戲需要用的部分，只是讓大家在執行時，有東西可以看，表示我們的伺服器有活著在動！
```
fun main(args: Array<String>): Unit = io.ktor.server.netty.EngineMain.main(args)

@Suppress("unused") // Referenced in application.conf
@kotlin.jvm.JvmOverloads
fun Application.module(testing: Boolean = false) {
    install(ContentNegotiation) {
        gson()
    }
    val client = HttpClient(Apache) {
    }

    routing {
        get("/") {
            call.respondText("HELLO WORLD!", contentType = ContentType.Text.Plain)
        }

        get("/html-dsl") {
            call.respondHtml {
                body {
                    h1 { +"HTML" }
                    ul {
                        for (n in 1..10) {
                            li { +"$n" }
                        }
                    }
                }
            }
        }
    }
}
```

### 編譯執行
想要執行程式跑出網頁可以利用右邊的Gradle列，點開後，點一下大象icon，輸入gradle run
![https://yayachang.github.io/ithome2020/images/day22-07.png](https://yayachang.github.io/ithome2020/images/day22-07.png)

接著IDE底下的Run視窗會開始進行編譯並執行程式，會產生一個http://0.0.0.0:8000的網頁位置。

![https://yayachang.github.io/ithome2020/images/day22-08.png](https://yayachang.github.io/ithome2020/images/day22-08.png)

點擊後，會出現"Hello World!"的字串，表示你的Ktor伺服器程式成功跑起來了！

![https://yayachang.github.io/ithome2020/images/day22-09.png](https://yayachang.github.io/ithome2020/images/day22-09.png)

其他的網頁路徑，/html-dsl 也一樣有資料顯示出來。

![https://yayachang.github.io/ithome2020/images/day22-10.png](https://yayachang.github.io/ithome2020/images/day22-10.png)

### 總結
只要幾個步驟，你的第一隻後端Hello World!程式就完成了，用Ktor是不是超級快速又方便! 接下來的篇章就能再介紹怎麼在伺服器安裝資料庫跟使用資料庫來存取遊戲的排行榜資料庫囉！

### 參考文章
* [https://ktor.io/](https://ktor.io/)
* [https://link.medium.com/A0ikeMDnV9](https://link.medium.com/A0ikeMDnV9)
