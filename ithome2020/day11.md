# [Day11] 使用音效Audio讓遊戲有聲音
遊戲的音效在遊戲中也是很重要的一環，如果精心設計的美術圖片跟場景是帶給玩家視覺的饗宴，那遊戲裡的音樂跟音效就是聽覺的饗宴了！應該也有人有經驗是還沒看到遊戲的畫面，先聽到它的背景音樂或是音效，或是角色的聲音，就能辨識出是哪一款遊戲。

### KorGE Audio
KorGE這次使用音效的功能是同一系列korlibs的KorAu，一樣都是由Kotlin打造的audio library，其中支援的音效格式有WAV、MP3 、OGG。

遊戲中通常有分音樂跟音效檔案，音樂通常是我們常講的背景音樂，會持續一小段時間進行循環播放，或是遊戲有劇情故事，會有一小段的台詞的聲音。而音效檔案有時是介面上點擊按鈕時產生的聲音，或是一些物體碰撞，掉落的聲音，會搭配畫面的特效一起產生的聲音。

### 播放音樂
將你的音樂檔案放置src/commonMain/kotlin/resources

![https://yayachang.github.io/ithome2020/images/day11-01.png](https://yayachang.github.io/ithome2020/images/day11-01.png)

寫這一行播放音樂的程式碼，先讀取音樂檔案，隨後播放。
```
val music = resourcesVfs["music.mp3"].readMusic()
music.play()
```

只會播一次這樣寫

```
music.play()
```

想要指定播放次數可以這樣寫
```
music.play(2.playbackTimes)
```

想要無限循環播放就這樣寫
```
music.play(PlaybackTimes.INFINITE)
```

如果想要終止聲音就比較麻煩點，需要先把聲音分配給channel，透過channel來停止。
```
val music = resourcesVfs["music.mp3"].readMusic()
val channel = music.play(PlaybackTimes.INFINITE)
channel.stop()
```
### 總結
其實一般來說，播放音樂的功能基本應該要有play, pause, stop，但是暫時沒有發現暫停功能，還有也沒發現怎麼resume play。

因此目前先暫時向官方發問怎麼實現這些動作，若之後官方有消息會再更新發佈喔。筆者很認真的跑去問作者，也有幫忙加入issue了https://github.com/korlibs/korau/issues/28

### 最近更新- 2020/11/30 KoreGE 2.0
開發團隊已經加入暫停跟回復播放的功能了！
https://github.com/korlibs/korau/releases/tag/v2.0.0
所以有使用到音樂或音效的朋友們，把KoreGE library升級到2.0就可以囉。
以下範例就自己依情況使用拉

```
val music = resourcesVfs["music.mp3"].readMusic()
val channel = music.play(PlaybackTimes.INFINITE)
channle.stop()//停止
channel.pause()//暫停
channle.resume()//從暫停處再播放
```

### 參考文章
* [https://korlibs.soywiz.com/korge/reference/audio/](https://korlibs.soywiz.com/korge/reference/audio/)
