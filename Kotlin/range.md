# Range

Range這個類別我覺得算是在Kotlin裡很好用的東西

可以很簡單明瞭得透過程式表示一個數字範圍

例如以前用Java想要表示1到10，你可能就要寫
```
for(int i =1; i <=10; i++){
    println("i:"+i)
}
```
現在用Kotlin，你可以寫成
```
for(i in 1..10){
    println("i:${i}")
}
```
如果是對初學者來說，我想Kotlin會非常簡單易懂

Range也提供了一些好用的方法

### until
以前要取得陣列的內容寫迴圈<=都要特別記得count-1要不就是要記得寫<, 不然忘記就很容易會index outOfRanage，現在有until就不用寫count-1了。

Java這樣寫
```
int[] intList = new int[]{1, 2, 3};
for(int i =0; i < intList.length; i++){
    System.out.println("i:"+i);
}
for(int i =0; i <= intList.length-1; i++){
    System.out.println("i:"+i);
}
```
Kotlin用until這樣寫
```
val intList = intArrayOf(10, 20, 30)
for (i in 0 until 3) {
    println("value:${intList[i]}")
}
```
仔細看until的原始就是幫你把傳入的數值-1
```
public infix fun Int.until(to: Int): IntRange {
    if (to <= Int.MIN_VALUE) return IntRange.EMPTY
    return this .. (to - 1).toInt()
}
```
### indices
陣列也可以用indices方法表示（相當於變成0..2)
```
for (i in intList.indices) {
    println("value:${intList[i]}")
}
```
可以看一下陣列的indices方法，就知道為什麼是i in 0..2
```
public val IntArray.indices: IntRange
    get() = IntRange(0, lastIndex)
```
如果是第一次學程式的，對於小於，小於等於這些邏輯再搭配陣列長度，有時候都很容易不好理解，不過Kotlin有點用平常寫英文的方式來表示，可能會比較簡單好理解了。

其他的寫法像是順著數，倒著數都能用類似很口語的方法來表示

### rangeTo
順著數
```
for(i in 1.rangeTo(10)){
    println("i:${i}")
}
```
### downTo
倒著數
```
for(i in 10.downTo(1)){
    println("i:${i}")
}
```
### reversed
還有順著之後加上reversed方法又可倒著數
```
for(i in 1.rangeTo(10).reversed()){
    println("i:${i}")
}
```
### step
還能跳著間隔計數
```
for(i in 1.rangeTo(10).step(2)){
    println("i:${i}")
}
```
