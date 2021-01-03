# [Day30] 使用KorGE的開發心得

這次三十天的用KorGE開發自己的Kotlin小遊戲終於進到最終篇章囉！ 相信各位對KorGE有一些些的認識，已經可以試著用這套遊戲引擎來開發你的小遊戲。

### KorGE使用心得
我認為做為開發輕量型的小遊戲是很適合，還可以跨平台。但是畢竟這個引擎還算是開發階段，所以在這三十天也是有遇到一些小問題，像是音效沒有暫停繼續播放機制，或是在倒數第一篇串接AdMob廣告就失敗沒有辦法使用，還有開發文件的Sample並不多或是一些片段，有的是我要猜猜看，試試看才能把功能弄出來，有問題也必須要厚臉皮直接去問開發團隊，因為搜尋在stackoverflow 搜尋KorGE文章是非常少的，可能對一開始學遊戲開發的朋友會有點吃力。不過我這次參賽的目的就是藉由研究KorGE來做出這個小遊戲專案，也順便練習用Kotlin寫程式，當作自己的開發筆記，也算先替大家踩一些坑，讓大家決定要不要一起來入坑。

### 歸納學習的重點
我這裡整理了這三十天以來學了哪些東西，幫大家複習一下：

**遊戲開發環境介紹：**

(1) 安裝KorGE專案

[Day01](https://yayachang.github.io/ithome2020/day01)

(2)安裝Ktor的開發環境

[Day22](https://yayachang.github.io/ithome2020/day22)

(3)MySQL資料庫安裝

[Day23](https://yayachang.github.io/ithome2020/day23)

**KorGE遊戲元件介紹：**

(1)用Text，Font在遊戲顯示文字

[Day05](https://yayachang.github.io/ithome2020/day05)

[Day06](https://yayachang.github.io/ithome2020/day06)

(2)用AddHrUpater, Tween, Sprite Animation的動畫特效

[Day07](https://yayachang.github.io/ithome2020/day07)

[Day08](https://yayachang.github.io/ithome2020/day08)

[Day09](https://yayachang.github.io/ithome2020/day09)

(3)Keyboard, Touch, Mouse的遊戲輸入

[Day10](https://yayachang.github.io/ithome2020/day10)

(4)遊戲音效播放

[Day11](https://yayachang.github.io/ithome2020/day11)

(5)Scence場景切換、Image圖片、畫面解析度Resolution設定

[Day03](https://yayachang.github.io/ithome2020/day03)

[Day04](https://yayachang.github.io/ithome2020/day04)

[Day12](https://yayachang.github.io/ithome2020/day12)

(6)運用碰撞偵測

[Day18](https://yayachang.github.io/ithome2020/day18)

**遊戲伺服器設計(Ktor)**

(1)運用Exposed存取資料庫

[Day24](https://yayachang.github.io/ithome2020/day24)

(2)Server端的API設計

[Day25](https://yayachang.github.io/ithome2020/day25)

**遊戲客戶端設計(KorGE)**

(1)實作出遊戲架構(Splash, Menu, GamePlay, GameOver, Rank)

[Day02](https://yayachang.github.io/ithome2020/day02)

[Day13](https://yayachang.github.io/ithome2020/day13)

[Day14](https://yayachang.github.io/ithome2020/day14)

[Day15](https://yayachang.github.io/ithome2020/day15)

[Day16](https://yayachang.github.io/ithome2020/day16)

[Day17](https://yayachang.github.io/ithome2020/day17)

[Day19](https://yayachang.github.io/ithome2020/day19)

[Day20](https://yayachang.github.io/ithome2020/day20)

[Day21](https://yayachang.github.io/ithome2020/day21)

(2)Client端的API串接

[Day26](https://yayachang.github.io/ithome2020/day26)

[Day27](https://yayachang.github.io/ithome2020/day27)

(3)發佈在不同平台上(Web, Mobile, Desktop)

[Day28](https://yayachang.github.io/ithome2020/day28)

(4)廣告串接(還在進行中….)

[Day29](https://yayachang.github.io/ithome2020/day29)


### 專案GitHub程式碼：

https://github.com/yayachang/korege_ithome

大致上的學習重點都列出了，當然如果你看官方文件還是有很多東西我沒有介紹到，主要也是介紹以我的小遊戲能實作出來運用到的部分。

介紹一些用KorGE做出來的小遊戲

類似Flappy Bird的專案 

https://github.com/SerVB/korge-zombie-bird

也有開發者做出2048(也是KorGE開發團隊的人)，也寫了開發blog

https://blog.korge.org/korge-tutorial-writing-2048-game-step-0/

團隊為了推廣也有舉辦一屆比賽，還有獎金可拿，不知道下次舉辦是什麼時候。

https://itch.io/jam/korge-gamejam-1

### 贊助支持KorGE
剛剛說了，KorGE還算發展階段，也是一些熱愛程式跟遊戲的人一起幫忙開發的專案，所以如果有錢出錢，沒錢的就是幫忙推廣使用。

https://www.patreon.com/korgegameengine

### 參賽心得
很累但是也很充實，因為要準備30天的文章並不容易，大概接近在15天左右有快虛脫的感覺(上班＋趕稿壓力)，而且有大部分的時間都是在寫程式實作遊戲專案，試出自己介紹的那些KorGE的功能。 不過正因為是順便寫了一個小遊戲，所以在文章的內容上是比較好發揮的。

如果下次還要繼續參加鐵人賽，應該是從現在開始就要規劃，可能一個月寫三篇，才能在明年悠哉每日上傳，但是技術性的文章有時候又很看時效性，所以不見得一年前寫的東西到隔年熱度還在(或是官方文件網頁位置有更動就要更新文章了)，不過明年的事明年再說，把握當下才是最實際的囉！

### 特別感謝
這次跟的「Kotlin 鐵人陣」團隊，大部分都是從Kotlin讀書會的夥伴，大家一起在這三十天一起每天分享發文，互相激勵(吐槽)，才能渡過這艱難的關卡。歡迎對Kotlin有興趣的讀者也能加入學習Kotlin的行列囉。https://tw.kotlin.tips/ ，一起學習效果顯著！
