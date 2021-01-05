# Char
char 以前剛開始學的時候念char(洽)就被糾正，所以就都會唸全名character。

char用來表示單字，像是英文字母的大小寫，a到z跟A到Ｚ。
還有可以用跳脫字元＋一些單字組變成特殊用途
像是Tab空格: \t，換行:\n

比較常用但有時候會搞錯的
像是單引號'\'跟雙引號'"'
跟正斜線 '/'及反斜線 '\'

舉例：（印出來的前跟後加--，不然怕看不太出來)
```
var tab = '\t'
var line = '\n'
var quote = '\'
var double_quote = '"'
var slash = '/'
var backslash = '\\'

println("value:--${tab}--")
println("value:--${line}--")
println("value:--${quote}--")
println("value:--${double_quote}--")
println("value:--${slash}--")
println("value:--${backslash}--")
```
結果：
```
tab:              --    --
line:             --
--
quote:            --'--
double_quote:     --"--
slash:            --/--
backslash:        --\--
```
