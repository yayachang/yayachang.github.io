# 使用Palette根據圖片顏色來顯示字體的顏色跟背景色
這次用藍色的哆拉A夢跟黃色的哆拉A夢來Demo!

![https://yayachang.github.io/Kotlin/images/andoroid_palette.png](https://yayachang.github.io/Kotlin/images/andoroid_palette.png)

踩雷的部分是，我以為只要取vibrantSwatch就好，結果發現有的圖根本沒有vibrant的色調，只好再去找，就這樣一路判斷到有色調，截出來字跟背景才正常顯示。

### 程式碼
```
private fun getSwatch(palette: Palette): Swatch? {

    val vibrantSwatch: Swatch? = palette.vibrantSwatch

    val darkVibrantSwatch: Swatch? = palette.darkVibrantSwatch

    val lightVibrantSwatch: Palette.Swatch? = palette.lightVibrantSwatch

    val mutedSwatch: Swatch? = palette.mutedSwatch

    val darkMutedSwatch: Swatch? = palette.darkMutedSwatch

    val lightMutedSwatch: Swatch? = palette.lightMutedSwatch

    when {

        //獲取到充滿活力的這種色調
        vibrantSwatch != null -> {

            return vibrantSwatch
        }

        //獲取充滿活力的黑
        darkVibrantSwatch != null -> {
            return darkVibrantSwatch

        }
        //獲取充滿活力的亮
        lightVibrantSwatch != null -> {
            return lightVibrantSwatch

        }
        //獲取柔和的色調
        mutedSwatch != null -> {
            return mutedSwatch

        }
        //獲取柔和的黑
        darkMutedSwatch != null -> {
            return darkMutedSwatch

        }
        //獲取柔和的亮
        lightMutedSwatch != null -> {
            return lightMutedSwatch

        }
    }
}
```
