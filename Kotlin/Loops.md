# Loops

在程式設計裡迴圈也是常用的語法之一，主要運用在需要處理有重複結構的事件，而常見的關鍵字就是while跟for。

# while

while的寫法如下：
```
while(條件式){
  //要做的事情
}
```
通常while的使用情境會是有一個需要達成的條件，一旦達成後就會不斷地處理。

舉例：
永不停止地說Hello!(通常不建議這樣做，會讓你的電腦當機啊！)
```
while(true){
  println("Hello!")
}
```
或是另外一例子，倒數5秒，印出倒數秒數。
```
var count = 5
while(count >= 0){
  println("倒數${count}秒")
  count-=1
}
```

印出結果：
```
倒數5秒
倒數4秒
倒數3秒
倒數2秒
倒數1秒
倒數0秒
```
# for

for通常會用在有限制的數量條件，在滿足數量之前會重複處理。
舉例：從1加到10(在i計數到10之前都會將總數一直加總)
```
var sum = 0
for(i in 1..10){
  sum += i
}
println("加總：${sum}")
```
印出結果：
```
加總：55
```

另一個常用的方法是，只要是陣列的物件，都能直接用以下的寫法：
```
for(物件變數 in 陣列物件){
  \\....todo
}
```
舉例：
```
val nameList = arrayOf("Yaya", "Kotlin", "Korge")
for(name in nameList){
  println("你的名字：${name}")
}
```
印出結果：
```
你的名字：Yaya
你的名字：Kotlin
你的名字：Korge
```
