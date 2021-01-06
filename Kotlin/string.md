# String

String 應該就是超常見的類型了，從第一天學程式"Hello World!"就是把字串印出來。
```
val firstWord = "Hello World!"

println(firstWord)
```
執行結果
```
Hello World!
```
也可以把在char學的\n換行字元放到字串裡，達到換行效果。
```
val stringWithNewLine = "Hello World!\nHello Kotlin!"

println(stringWithNewLine)
```
執行結果
```
Hello World!
Hello Kotlin!
```
如果不想要寫\n換行，可以用一對三個單引號，直接可以按Enter做換行動作。
```
val threeQuoteString = """

用三個單引號可以直接按Enter換行""" 

println("threeQuoteString:${threeQuoteString}")
```

執行結果
```
threeQuoteString:
    用三個單引號可以直接按Enter換行
```

因為直接用一對雙引號加Enter換行，會編譯不過出現錯誤，如下圖：
![](https://yayachang.github.io/Kotlin/images/string_error.png)
