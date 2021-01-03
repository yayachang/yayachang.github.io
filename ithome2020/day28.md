# [Day28] 輸出到不同平台-Android, iOS, Web
用KorGE開發遊戲的優點之一是可以跨平台，可以輸出到桌機、Web網頁跟Mobile手機，所以這一篇就要來介紹怎麼把做好的外星人跑跑Alien Run輸出到這些平台上執行囉。

### 輸出到Web
只要輸入以下gradle指令，IDE就會自動幫你把web server叫起來執行。
```
./gradlew runJs
```
執行結果：

![https://yayachang.github.io/ithome2020/images/day28-01.png](https://yayachang.github.io/ithome2020/images/day28-01.png)

### 輸出到Android
記得先準備測試的Android手機或是去Android Studio把一個模擬器開起來，執行下列指令就會幫你裝進去囉。
```
./gradlew installAndroidDebug
```
但是裝到手機上的預設App名稱是unamed，App Icon也不會是預期的，所以要自己額外去AndroidManifest.xml更改。
模擬器執行結果：

![https://yayachang.github.io/ithome2020/images/day28-02.png](https://yayachang.github.io/ithome2020/images/day28-02.png)

實機執行結果(我的Pixel 3)：

![https://yayachang.github.io/ithome2020/images/day28-03.png](https://yayachang.github.io/ithome2020/images/day28-03.png)

### 輸出到iPhone
因為筆者沒有iphone手機，所以實際裝到iphone手機的情形，就讓有iphone手機的讀者試試看效果怎麼樣囉。
不過筆者有MAC電腦，所以可以安裝XCode來試著輸出到模擬器。
```
./gradlew installIosSimulatorDebug
```
執行結果：
畫面有跑出來，但是我試了按了模擬器好像等級沒反應，不知道放到手機上是不是就正常了？

### 總結
在跑Android模擬器跟Ｗeb執行時，好像外星人走路delay很嚴重，但是放在手機上執行確實很正常，目前還在尋找問題，或是在GamePlay的設計上可以再更好(可能圖片再壓縮，或是寫法再改進)，優化跟找問題就是持續進行中，但是至少告訴大家KorGE是可以能跑在Web跟Mobile平台上囉。

### 參考文章
* [https://korlibs.soywiz.com/korge/deployment/](https://korlibs.soywiz.com/korge/deployment/)
