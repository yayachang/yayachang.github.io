# Numbers

Kotlin的類別基本上都是用物件 Object 表示，意思就是宣告Kotlin的變數類型基本上都可以給null。

### Numbers是Kotlin內建類型的其中一個種類，分別有以下：

|**類型**	|**長度**	|
|-	|-	|
|Long	|64bit	|
|Int	|32bit	|
|Short	|16bit	|
|Byte	|8bit	|
|Double	|64bit	|
|Float	|32bit	|

以我的工作經驗來說，最常用的當然是Int(一般數字存取）, 再來是Long（多用在存timestamp)，再來是Double跟Float存小數點數值，Byte比較常用在直接用WiFi或是藍牙傳輸raw data，像韌體工師通常都是16進位Hex思維設計程式，所以通常都會用Byte，而Short就真正在實戰上比較少用了。

### Long範例：
```
var timestampe = 1608595045L
```
記得在程式在數字的結尾要加上大寫L，才會被認得是Long型態

### Int範例：
```
var int_number = 1234
```
Integer就一般很常用的囉，應該大家用起來沒什麼問題

### Short範例：
```
var short_number = 1234
```
Short(16bit)就是比較小的Integer(32bit)，能表示的數值比Integer少

### Double範例：
```
var double_number = 1.234
```
如果有小數點的數值，用Double就可以囉

### Float範例：
```
var float_number = 1.234F
```

如果你想存超級長的小數點數值就用Float，記得尾巴要加上大寫F

### Byte範例：
```
var byte_number = 0b00010110
```
如果你的需求會有要處理二進位，就能使用byte，像這個範例就是整數22，只是用二進位表示，在開頭加上0b然後再加上8個位元的00010110

```
var hex_number = 0x16
```
如果要處理十六進位，一樣數值是整數22，換十六進位表示，在開頭加上0x然後再加上2個位元的16
