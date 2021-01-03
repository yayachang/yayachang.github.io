# [Day29] 在KorGE加入廣告
KorGE開發團隊有設想到大部分遊戲還是都會用廣告來當作營收管道，所以有幫忙寫了一個AdMob的Plugin。

### AdMob廣告
AdMob是全球最大規模的廣告聯播網之一，能幫你的應用程式拉廣告給用戶觀看，進而讓你的APP獲得收益，若想知道更詳細的介紹就到官網去逛逛囉。

[AdMob網站：https://admob.google.com/intl/zh-TW/home/](https://admob.google.com/intl/zh-TW/home/)

(這裡就不介紹怎麼在AdMob開帳號跟建立應用程式的APP ID，相信官網的教學大家很快就能學會怎麼產生AdMob的APP ID)。

因為筆者主要是開發Android，所以就帶大家試試看怎麼在KorGE加上AdMob的套件。
在 build.gradle.kts檔案加入你在AdMob申請的APPID。
```
korge {
    admob(ADMOB_APP_ID) // Shortcut for admob
}
```
在程式的進入點產生Admob instance。
```
injector.mapSingleton { AdmobCreate(testing = true) }
```

### 怎麼在遊戲裡放廣告？
為了不干擾遊戲體驗，通常盡量不要放在會影響玩家操作的畫面，但是又希望廣告能放在玩家停留時間久的地方，所以有時候開發者為了混口飯吃還是不得已把廣告放在大家不喜歡的地方(如果是玩免費遊戲就體諒一下開發者吧…)，然後另一個放置的時機點可能就是結束一回合的時候，通常都會安插一個全頁廣告，這個也是通常很讓玩家反感的做法(但是但是都說了開發者也是要混口飯吃嘛….)，所以拿捏放廣告的時機跟位置也是一門學問，可能要調整好幾次才能達到最佳位置跟最佳時機。

### 橫幅廣告
這次我們試試看的有最常見的橫幅廣告 ，通常會稱作BANNER，呼叫方式也是很簡單，給它秀出來就對了。
```
AdmobCreate(testing = true).bannerShow()
```
正當我把Code貼好要show橫幅廣告的時候，程式IDE都沒有提醒有錯誤，直到按下前一篇的Android平台輸出。
登愣～怎麼會出現error呢？！```Unresolved reference:admob```

![https://yayachang.github.io/ithome2020/images/day29-01.png](https://yayachang.github.io/ithome2020/images/day29-01.png)

明明Project內的Admob library也有出現，可是就是編譯不過，只好用破英文去向開發團隊求救。

![https://yayachang.github.io/ithome2020/images/day29-02.png](https://yayachang.github.io/ithome2020/images/day29-02.png)

開發團隊的負責人告訴好像是Regression bug，所以請我去加個一新issue。
* https://github.com/korlibs/korge/issues/293

### 總結
這一次非常殘念，沒辦法真正直接在KorGE加廣告給大家看，所以這次的研究就算是不太順利，不過也暫時只能先等開發團隊修好issue囉！

### 參考文章
* [https://korlibs.soywiz.com/korge/reference/monetization/](https://korlibs.soywiz.com/korge/reference/monetization/)
