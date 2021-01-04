# 貓王運算子 Elvis Operator ?: 

第一次看到這個符號 ?: 就是是在學Kotlin的時候

看圖說故事就知道為什麼叫貓王了(帥到連寫Code的人都要致敬 XDDD)

(自己去google Elvis Operator的圖片就知道了拉！)

但這都不是重點啊, 主要是?: 的用法

當你的變數是空值時，就會要使用?:接續的那個數值
```
var test1:String  = "我不是空值"
var test2:String? = null
var result1 = test1 ?: "我是空值"
var result2 = test2 ?: "我是空值"

println("result1為"$test1)
println("result2為"$test2)
```
這樣還不了解的話，沒關係，只要拆解開來就能了解了！

那就用if判斷式來解釋
```
var test1:String  = "我不是空值"
var test2:String? = null

var result1 = test1
if(result1 == null){ 
    result1 = "我是空值"
}

var result2 = test2
if(result2 == null){ 
    result2 = "我是空值"
}

println("result1為"$test1)
println("result2為"$test2)
```
印出結果都會是:
```
result1為我不是空值
result2為我是空值
```
結論是：
用了耍帥的?: 貓王符號可以寫比較少的Code!!
