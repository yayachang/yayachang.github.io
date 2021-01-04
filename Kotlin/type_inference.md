# Type inference

硬要翻中文好像叫做"類型推論"，顧名思義就是Kotlin編譯時會根據你給予的數值來推論你寫的數值類型。

如果你的第一個語言是學Kotlin可能覺得，下面這行Code這樣寫沒什麼問題，
```
var name = "Hello Kotlin"
```
但是像是一些會要強制你宣告變數類型的程式就不能這樣做，例如Java你就一定要特別寫String，不然在Java裡就會出錯。
```
String name = "Hello Kotlin";
```


當然在Kotlin你還是可以幫忙加上String類型囉。
```
var name:String = "Hello Kotlin"
```
而Type inference不限於只有用在var跟val，用在function也一樣有同樣的效果，像以下的範例就是因為傳入值x有先宣告為Int整數，所以回傳結果的x+1也會是Int整數類型，但是因為是Type inference關係，在Kotlin沒有寫回傳類型，也是被允許的。
```
fun plusOne(x: Int) = x + 1
```
也可以寫成
```
fun plusOne(x: Int):Int = x + 1
```
