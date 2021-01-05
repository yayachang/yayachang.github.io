# Boolean

Booleans中文叫布林值
就是很常用在條件式判斷，就是if else囉。

### 是真的範例
```
val isTrue = true
```
### 是假的範例
```
val isFalse = false
```
### 用在判斷式的OR用兩個槓槓表示 (稱作lazy disjunction)
```
val isLazyDisjunction = isTrue || isFalse
```
### 用在判斷式的AND用兩個&&表示 (稱作lazy conjunction)
```
val isLazyConjunction = isTrue && isFalse
```
### 否定符號!(稱作negation)
```
val isNotTrue = !isTrue
```
工作上常用的Boolean可能就是用在隱藏打開UI的畫面

### 舉例：Android的View的顯示隱藏
```
view.visibility = if(isToShown) VIEW.VISIBLE else VIEW.GONE
```
或是判斷某個數字是否達到上限
### 舉例：體重是否超過標準體重
```
val isStandardWeight = myWeight > 58
```
不然就是兩件事情要同時成立時的判斷
### 舉例：同時是雨天且又沒帶傘
```
val isBad = isRanningDay && NoUmbrella
```
或是兩件事情只要一個成立就好
### 舉例：只要英文或數學成績達標
```
val isReachGoal = isEnglishScore > 60 || isMathScore > 60
```
