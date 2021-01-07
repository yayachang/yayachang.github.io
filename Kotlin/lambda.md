# [Anonymous Function] 匿名函式、Lambda

### 匿名函式練習:
```
fun main() {
    
   val stringType : String ="Hello Kotlin!!!" //字串類型
    
   //匿名函式，輸入傳入參數為name類別為String, 並回傳String 
   val function1 : (name:String)-> String = {
       name->
       "${name} Hello Kotlin!!!"
   }
   
    //匿名函式，輸入傳入參數為name類別為String, 用it關鍵字表示name, 並回傳String 
   val function1_1 : (name:String)-> String = {
       "${it} Hello Kotlin!!!"
   }
   
    //匿名函式，輸入傳入參數隱藏，類別為String,但是裡頭還是將傳入參數表示用name, 並回傳String 
    val function1_2 : (String)-> String = {
       name->
       "${name} Hello Kotlin!!!"
   }
    
   //匿名函式，輸入傳入參數隱藏，類別為String,但是裡頭用it關鍵字表示name, 並回傳String 
   val function1_3 : (String)-> String = {
       "${it} Hello Kotlin!!!"
   }
   
   //類型推斷，在函式的傳入參數名稱name跟類別寫出來，
   //這樣原本function2緊接的:(String)的參數表示方式
   //就能由函式裡的name:String推論出來，因此可以隱藏。
   val function2 = { name:String ->
       "${name} Hello Kotlin!!!"
   }
   
   println("stringType:${stringType}")
   println("function1  :${function1("GOGO!")}")
   println("function1_1:${function1_1("GOGO!")}")
   println("function1_2:${function1_2("GOGO!")}")
   println("function1_3:${function1_2("GOGO!")}")
   println("funciton2  :${function2("GOGO!")}")
}
```

執行結果:
```
stringType:Hello Kotlin!!!
function1  :GOGO! Hello Kotlin!!!
function1_1:GOGO! Hello Kotlin!!!
function1_2:GOGO! Hello Kotlin!!!
function1_3:GOGO! Hello Kotlin!!!
funciton2  :GOGO! Hello Kotlin!!!
```

### Lambda的定義
```
(paramter type ) -> return type
(<arg1>, <arg2>) -> <return>

```
ex:
```
val lambda_test: (x:Int, y:Int) -> Int = { x:Int, y:Int -> x + y }
```
### Lambda invoke
```
val lambda_invoke_test = lambda_test.invoke(3,4)
val lambda_invoke2_test = lambda_test(3,4)
println("lambda_invoke_test:${lambda_invoke_test}")
println("lambda_invoke2_test:${lambda_invoke2_test}")
```

### 匿名函式+Lambda練習
```
//Anonymous Function
val anonymousFunction_1: (value:Int)-> Unit = { value:Int -> println("result is ${value*10}")}
val anonymousFunction_2: (value:Int)-> Unit = { println("result is ${it*10}")}
val anonymousFunction_3: (Int)-> Unit = { println("result is ${it*10}")}

//Lambda Function, value要給類型不然不知道value是什麼
val lambdaFunction_1 =  { value:Int -> println("result is ${value*10}")}

fun handleInteger(myInt:Int, operation:(Int)->Unit){
  operation(myInt)
}

handleInteger(5, anonymousFunction_1)
handleInteger(5, anonymousFunction_2)
handleInteger(5, anonymousFunction_3)
handleInteger(3, lambdaFunction_1)
```
執行結果:
```
result is 50
result is 50
result is 50
result is 30
```

### 把一般的具名函式named Function變成Lambda來用，就是把具名函式前面多加兩個冒號::
```
fun printResult(value:Int){
	println("My Int is $value")
}
handleInteger(2, ::printResult)
```
執行結果:
```
My Int is 2
```
