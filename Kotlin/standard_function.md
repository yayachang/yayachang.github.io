# Standar Functions

### apply
減少redundant code

舉例:
```
val file = File(“test.txt”)
file.setReadable(true)
file.setWritable(true)
file.setExecutable(false)
```
### 用apply改寫
```
val file = File(“test.txt”).apply{
	setReadable(true)
	setWritable(true)
	setExecutable(false)
}
```

### let
將某個變數作用於他的lambda運算式，可以使用it關鍵字

舉例：
```
val firstElement = listOf(1,2,3).first()
val firstItemSquared  = firstElement * firstElement
```

### 用let改寫
```
val firstItemSquared = listOf(1,2,3).first().let{
	it*it
}
```


### let跟apply的不同
let會回傳執行完函式的最後一行
apply回傳給receiver

### run
run跟apply相似，但不回傳給receiver，是回傳執行完的最後一行
舉例：
```
val file = File(“test.txt”)
val checkContainsWords = file.run{
	readText().contains(“Hello World”)
}
```
### with
With是run的變形，功能跟行為一樣，只是with要求引數作為第一個參數傳入，也是回傳執行完的最後一行

舉例：
```
“Hello World”.run
with(“Hello World)
```
### also
跟let有些相似，但是also返回receiver object, let返回lambda結果

### takeIf
需要判斷lambda中提供的條件運算事(predicate)再給出true, false的結果
如果是true->返回receiver，false回傳null

舉例:
```
val file = File(“test.txt”)
val fileContents = if(file.canRead() && file.canWrite()){
	file.readText()
}else{
	null
}
```
改寫
```
val fileContents =  File(“test.txt”).takeIf{ it.canRead() && it.canWrite()}?.readText()
```

### 用lambda當傳入引數，也就是用it關鍵字 => let, also, takeIf
### 沒用lambda當傳入引數，使用relative scoping =>apply, run, with

### 返回
lambda結果-> let, run, with

receiver 物件->apply, also

可以是NULL的receiver物件-> takeIf
