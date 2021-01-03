# [Day23] 建立遊戲資料庫-安裝MySQL

已經安裝好Ktor了，接下來要建置的就是資料庫的部分，雖然選擇的資料庫類型有很多，不過還是用筆者常用的MySQL來當作練習示範好了。所以這一回的介紹比較偏向安裝教學的入門了，若已經會資料庫建置的讀者，可以期待下回的Ktor跟MySQL資料庫串接部分囉。

### 安裝MySQL
安裝檔下載(以Mac系統為例)

[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)

![https://yayachang.github.io/ithome2020/images/day23-01.png](https://yayachang.github.io/ithome2020/images/day23-01.png)

下載完dmg後，點開進行安裝

![https://yayachang.github.io/ithome2020/images/day23-02.png](https://yayachang.github.io/ithome2020/images/day23-02.png)

安裝精靈跳出，一直下一步就好，直到跳出要你輸入root的密碼，就輸入吧！

![https://yayachang.github.io/ithome2020/images/day23-03.png](https://yayachang.github.io/ithome2020/images/day23-03.png)

最後安裝成功就能按Close結束囉！

![https://yayachang.github.io/ithome2020/images/day23-04.png](https://yayachang.github.io/ithome2020/images/day23-04.png)

安裝完後，如果你是MAC電腦，可以到System Preference裡頭找到MySQL的設定，可以看到MySQL服務正在啟用當中。

![https://yayachang.github.io/ithome2020/images/day23-05.png](https://yayachang.github.io/ithome2020/images/day23-05.png)

安裝好了MySQL Server，當然要有可以建立資料庫跟存取資料庫的地方，這些動作當然你可以在程式裡頭寫好都能進行，但是通常我們會另外安裝一個MySQL的Client程式，不透過寫程式的方式來直接查看會比較方便測試開發。

### 安裝MySQL Workbench
好用的MySQL Client程式也是很多，但是我們還是直接下載官網提供的MySQL Workbench來使用就夠用了。
安裝檔下載(以Mac系統為例)
[https://dev.mysql.com/downloads/workbench/](https://dev.mysql.com/downloads/workbench/)

![https://yayachang.github.io/ithome2020/images/day23-06.png](https://yayachang.github.io/ithome2020/images/day23-06.png)

一樣下載好dmg點擊下載，這次就是把MySQL Workbench這個桌機APP拖曳到Application資料夾。

![https://yayachang.github.io/ithome2020/images/day23-07.png](https://yayachang.github.io/ithome2020/images/day23-07.png)

最後你可以在Lauchpad或直接去Application底下找到APP點擊就能打開MySQL Workbench囉。

![https://yayachang.github.io/ithome2020/images/day23-08.png](https://yayachang.github.io/ithome2020/images/day23-08.png)

打開後，照理說會幫你找到剛剛本機已經建立好的MySQL Server，會看到有Local instance 3306這一個區塊的資訊，如果沒有你也可以在MySQL Connections旁邊的加號➕點下去直接新增要連接的MySQL Server。

![https://yayachang.github.io/ithome2020/images/day23-09.png](https://yayachang.github.io/ithome2020/images/day23-09.png)

新增後會跳出一個"Setup New Connection"的視窗，Connection Name:可以取一個你比較好認得辨識的名稱例如"My Game Server"，Hostname就是你連接的MySQL Server的ip或domain name，Username是MySQL登入帳號、Password可以事先寫好儲存，或是選擇連接實再輸入都行。

![https://yayachang.github.io/ithome2020/images/day23-10.png](https://yayachang.github.io/ithome2020/images/day23-10.png)

Password想要存起來不用每次連就點選Store in Keychain…，輸入你的密碼。

![https://yayachang.github.io/ithome2020/images/day23-11.png](https://yayachang.github.io/ithome2020/images/day23-11.png)

連接MySQL Server後，會跳出一個大大的視窗，如果都不熟悉也沒關係，只要先確定你的Tab是連到剛剛我們編輯的"My Game Server"。

![https://yayachang.github.io/ithome2020/images/day23-12.png](https://yayachang.github.io/ithome2020/images/day23-12.png)

### 建立資料庫的Schema
這時候我們要先做一個動作就是建立Schema，有了Schema才能在裡頭建立資料庫的表Table。我們把Schema就稱作為"mygame"囉！
這時你要在視窗的左下角看到SCHEMAS這個區塊，在空白處按下滑鼠右鍵，會跳出選項，選擇"Create Schema"。

![https://yayachang.github.io/ithome2020/images/day23-13.png](https://yayachang.github.io/ithome2020/images/day23-13.png)

接著在視窗中間會跳出請你輸入Schema名稱的畫面，填好"mygame"後按下Apply。

![https://yayachang.github.io/ithome2020/images/day23-14.png](https://yayachang.github.io/ithome2020/images/day23-14.png)

MySQL Workben比較謹慎需要你再確認review過是不是真的要apply剛剛的動作。(不然有的指令下了一去不復返)。

![https://yayachang.github.io/ithome2020/images/day23-15.png](https://yayachang.github.io/ithome2020/images/day23-15.png)

接著回頭看視窗左手邊SCHEMA區塊，多出mygame了，不過當然內容都是空空的囉。

![https://yayachang.github.io/ithome2020/images/day23-16.png](https://yayachang.github.io/ithome2020/images/day23-16.png)

### 建立Table
接著我們來試著用MySQL Workbench來建立一個測試test Table，只是讓大家小試一番，因為實際的用Ktor撰寫資料庫方面的部分可以直接透過程式碼的定義就能做到建立Table了，這邊是讓大家練習熟悉怎麼手動用這套工具來建立Table。
建立Table只要選到Table然後滑鼠右鍵跳出選項，選擇Create Table。

![https://yayachang.github.io/ithome2020/images/day23-17.png](https://yayachang.github.io/ithome2020/images/day23-17.png)

這個簡單的test Table的主鍵就是id，然後我們另一個欄位就命名為name來存名稱字串， 按下apply產生Table。

![https://yayachang.github.io/ithome2020/images/day23-18.png](https://yayachang.github.io/ithome2020/images/day23-18.png)

一樣在視窗左手邊SCHEMA區塊，就多了Table →test →Coloums有了id跟, name。

![https://yayachang.github.io/ithome2020/images/day23-19.png](https://yayachang.github.io/ithome2020/images/day23-19.png)

大致上MySQL Workbench的教學就到這裡了，至於大家常說的CURD的指令，如果你還不會或是記不起來也沒關係，只要將滑鼠右鍵點選下去，會幫你產生CURD的指令樣板，就能輕鬆新增、更新、刪除、讀取資料了！

![https://yayachang.github.io/ithome2020/images/day23-20.png](https://yayachang.github.io/ithome2020/images/day23-20.png)

以下就是新增一筆資料，然後查詢出來的結果。

![https://yayachang.github.io/ithome2020/images/day23-21.png](https://yayachang.github.io/ithome2020/images/day23-21.png)

### 總結
現在一般建置開發比較少選擇用這種方式，因為通常會希望保持自己的電腦環境是裝越少東西越好，不要額外多裝這些開發環境，就算要裝也希望能騰出另外一台開發電腦，來裝這些設定，當然如果你的電腦夠力，裝一個VM的開發環境也是個選擇。 
有人會立刻說怎麼不使用Docker來裝MySQL就好了呢！？考慮到可能一些初學的人可能連Docker都不曾使用過，或是有的是使用Windows系統版本無法使用Docker，因此筆者選擇直接用安裝在本機的方式，少掉要學習Docker這一個門檻。
