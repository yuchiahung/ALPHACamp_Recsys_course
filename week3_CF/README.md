
# Week3 - Collaborative Filtering

## 專案目的

使用「使用者購買商品紀錄」以及「商品基本資訊」，以三種協同過濾 (Collaborative filtering) 的方式，為指定的使用者推薦 k 個其可能有興趣的商品，並以使用者**是否實際購買推薦商品**作為評價的指標。
三種協同過濾方法如下：
1. User-based collaborative filtering
2. Item-based collaborative filtering
3. 套件 surprise 實作 collaborative filtering

## 工具

Python, Google Colab

## 資料描述

- All_Beauty.csv ([link](http://deepyeti.ucsd.edu/jianmo/amazon/categoryFilesSmall/All_Beauty.csv))：提供使用者購買商品的評論紀錄
    - 資料區間：2000-01-10 - 2018-10-02 (共 371345)
    - 依據時間區間，拆分成訓練資料及測試資料：
        - 訓練資料：2000-01-10 - 2018-09-01 (共 370752)
        - 測試資料：2018-09-01 - 2018-09-30 (共 590)
- meta_All_Beauty.json.gz ([link](http://deepyeti.ucsd.edu/jianmo/amazon/metaFiles2/meta_All_Beauty.json.gz))：提供商品的基本資訊
    - 共 32892 個商品


## 推薦方法

### 方法1-1 依照範例程式手刻 user-based CF + cosine similarity：  
排除評論紀錄 < 3 次的使用者評論資料後，計算各個 user 之間購買物品的 cosine_similarity，最終推薦出 k 個商品（排除已買過的商品）

### 方法1-2 針對範例程式的 cosine_similarity 做部分更動  
由於計算 user-based CF 時發現大多使用者和其他使用者算出來的 cosine similarity 都是 = 1，探索之後發現是因為大多使用者和使用者之間僅有購買過一個相同的商品，因此在計算 cosine similarity 過程先將只有評價過一個共同商品的使用者排除。  
而針對只評價過一個共同商品的使用者，以 `overall` 評分數字的絕對差異來做相似度的比較，例如兩個使用者(A, B)如果都對同一個商品評價 5 分會比一個使用者評 5 一個使用者評 4 (A:5, B:4) 來得接近。
但以結果來說，此修正並沒有提升最終的分數。

### 方法2：Item-based CF + cosine similarity
依照範例程式，依照使用者對物品的評分，計算各個被購買物品之間的 cosine_similarity，最終推薦出相似的 k 個商品（排除以買過的商品不做推薦）

### 方法3-1：Surprise: Item-based CF + cosine + KNN
依照範例程式，但由於會超出記憶體的限制，在訓練資料的取用上限制在過去一年內 (2017-09-01~2018-08-31)

### 方法3-2：Surprise: Item-based CF + cosine + KNNBasic (min_support = 2) 
改自3-1，在使用 algorithm 的時候 （此處是使用 KNNBasic），將參數 `min_support` 設定為 2，也就是說至少要有兩個相同的使用者才會計算相似度，結果的分數較3-1提升了一倍左右  
(訓練資料區間: 2017-09-01~2018-08-31)

### 方法4：Surprise: Item-based CF + multiple combinations of similarities_module & KNN algorithm
使用 Surprise 套件，針對四種 similarities module 和四種 KNN algorithm 交叉實驗何者會有比較好的分數（共16種組合)，八種 similarities module & KNN algorithm分別如下:  
- Similarities_module: `cosine`, `msd`, `pearson`, `pearson_baseline`
- KNN algorithm: `KNNBasic`, `KNNWithMeans`, `KNNWithZScore`, `KNNBaseline`    
結果顯示此 16 種組合效果相似，分數並無差異。   
(訓練資料區間: 2017-09-01~2018-08-31)

### 方法5：結合方法 3-2 和 week1 的 rule-based 算法
week1 rule-based 算法：依據過去一個月的評論數量和平均評論分數排序，推薦排名前 k 個的熱銷品 


## 最終推薦分數  

成功推薦給使用者 k 個商品，最終以**方法3-2: 使用 Suprise item-based + Cosine Similarity + KNNBasic 並限制 min_support = 2**得到最高的分數：0.003390    
另外，若結合 week1 的 rule-based 推薦方式，能得到比 week1 (0.1576) 高的分數 (0.1593)，顯示由於此資料集的使用者購買次數不夠多，又無其他紀錄可參考（如瀏覽紀錄等），所以如果只使用協同過濾來做推薦的話，能夠預測出的購買行為很有限，在搭配推薦 rule-based 產生的熱銷品後會有較好的結果。 

五個方法的分數分別如下表：  
方法 | 分數
--- | ---
方法 1-1: sample 手刻 user-based | 0
方法 1-2: 1-1 將只有一個共同商品的使用者相似度往後排 | 0
方法 2: sample 手刻 item-based | 0.001695
方法 3-1: sample Surprise item-based + cosine + KNN | 0.001695
方法 3-2: Surprise item-based (min_support = 2)| 0.003390
方法 4: Surprise item-based (others similarities module & algorithm) | 0.003390
**方法 5: Surprise item-based + rule-based** | **0.159322**
