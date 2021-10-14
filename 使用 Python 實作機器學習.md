## 使用 Python 實作機器學習

### 前言

機器學習是一個設計演算法能夠學習的電腦，讓機器能夠透過觀察已知的資料學習預測未知的資料。典型的應用包含概念學習（Concept learning）、函數學習（Function learning）、預測模型（Predictive modeling）、分群（Clustering）與找尋預測特徵（Finding predictive patterns）。終極目標是讓電腦能夠自行提升學習能力，預測未知資料的準確性能夠隨著已知資料的增加而提高，節省使用者人工調整校正的精力。

### 什麼是scikit-learn?

scikit-learn 套件是專門用來實作機器學習以及資料採礦的，我們可以直接透過 scikit-learn 讀入 `digits` 資料，在網頁中的文字或文件，我們必須另外透過使用 `digits` 的屬性與方法來搜尋資訊。

`sklearn` 套件載入 `datasets` 模組，然後使用 `datasets` 模組的 `load_digits()` 方法來輸入資料

```python
# Import `datasets` from `sklearn`
from sklearn import datasets

# Load in the `digits` data
digits = datasets.load_digits()

# Print the `digits` data 
print(digits)
```

透過 `digits` 的 `keys()` 方法來得知有哪些基本資訊可以搜集；透過 `data` 屬性觀察預測變數；透過 `target` 屬性觀察目標變數；透過 `DESCR` 屬性閱讀資料的描述文件

```python
# Get the keys of the `digits` data
print(digits.keys())

# Print out the data
print(digits.data)

# Print out the target values
print(digits.target)

# Print out the description of the `digits` data
print(digits.DESCR)
```

使用 `matplotlib` 視覺化手寫數字圖片

```python
from sklearn import datasets
digits = datasets.load_digits()
import matplotlib.pyplot as plt

colors = ['black', 'blue', 'purple', 'yellow', 'white', 'red', 'lime', 'cyan', 'orange', 'gray']
for i in range(len(colors)):
    x = reduced_data_rpca[:, 0][digits.target == i]
    y = reduced_data_rpca[:, 1][digits.target == i]
    plt.scatter(x, y, c=colors[i])
plt.legend(digits.target_names, bbox_to_anchor=(1.05, 1), loc=2, borderaxespad=0.)
plt.xlabel('First Principal Component')
plt.ylabel('Second Principal Component')
plt.title("PCA Scatter Plot")
plt.show()
```

- 將不同的顏色儲存在一個 list 中。 由於相異的目標值有 10 個（0 到 9），所以我們指定了 10 種不同的顏色來標示。
- 設定 `x` 軸與 `y` 軸。 分別選出 `reduced_data_rpca` 的第一欄與第二欄，根據不同的目標值選出對應的觀測值，意即當迴圈開始的時候，會選出目標值為 `0` 的觀測值，接著是目標值為 `1` 的觀測值，依此類推。
- 畫出散佈圖。 當迴圈開始的時候，會將目標值為 `0` 的觀測值用黑色（`black`）畫出，接著是將目標值為 `1` 的觀測值用藍色（`blue`）畫出，依此類推。
- 使用 `target_names` 鍵值在散佈圖旁邊加上圖例。
- 加入圖形標題與座標軸標籤。
- 顯示圖形。

![scikitlearn1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/scikitlearn1.png>)

### K-Means 演算法

K-Means 演算法是最簡單且最廣泛被運用來解決分群問題的非監督式學習演算法。演算法首先隨意設定 `k` 個中心點，然後計算各個觀測值與這 `k` 個中心點的距離，將觀測值分配給距離最近的中心點貼上標籤，形成 `k` 個群集。接著這 `k` 個中心點的位置會被重新計算並移動到各個群集目前的中心，然後再重新計算各個觀測值與這 `k` 個中心點的距離，更新各個觀測值的群集標籤。



### 參考資料

https://www.datacamp.com/community/tutorials/scikit-learn-python
