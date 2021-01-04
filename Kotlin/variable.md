# var、val介紹

因為這裡的程式都比較簡單，所以我都用線上的Kotlin Playground來展示
https://play.kotlinlang.org/

### 變數
Kotlin有兩個關鍵字來定義變數，分別是val跟var。

var就是數值可以隨時再被修改的的變數，而val就是被定義後，就不能再被修改的常數。

### var的舉例：寫一個Hello World!，然後下一步再改成Hello Kotlin!
```
fun main() {
    var name = "Hello World!"
    println("name:${name}")
    name = "Hello Kotlin!"
    println("name:${name}")
}
```
執行結果
```
name:Hello World!
name:Hello Kotlin!
```

### val的錯誤舉例：寫一個Hello World!，然後下一步再改成Hello Kotlin!
```
fun main() {
    val name = "Hello World!"
    println("name:${name}")
    name = "Hello Kotlin!"
    println("name:${name}")
}
```
執行結果
```
Val cannot be reassigned
```

當然聰明的你一定發現我寫的val根本就是錯的啊，一開始就跟說了val是不能再被修改的常數，所以Playground Editor告訴你val不能再被給值，會出錯，所以如果你的程式已經明確知道有固定的數值，就直接用val囉。

### val的OK舉例：就只要寫一個Hello Kotlin!
```
fun main() {
    val name = "Hello Kotlin!"
    println("name:${name}")
}
```
執行結果
```
name:Hello Kotlin!
```
