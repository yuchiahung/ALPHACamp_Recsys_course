# Week3 - Matrix Factorization 

## 專案目的

使用「使用者購買商品紀錄」以及「商品基本資訊」，以 Surprise 和 LightFM 這兩個套件實現 Matrix Facotrization，為指定的使用者推薦 k 個其可能有興趣的商品，並以使用者**是否實際購買推薦商品**作為評價的指標。


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

## 資料前處理

商品基本資訊 `meta_All_Beauty.json`：

- 空值及商品基本資訊重複的處理
- 銷售排行 `rank` 的處理，其中移除了非屬 Beauty & Personal Care 類別的商品 （數量僅佔全部商品的大約 1.5%)，另外將 rank 的數字依據 percentile 切分成五個等級
- 價格 `price` 數值處理，並依據 percentile 切分成 5 個等級
- 文字資料 (`brand`, `title`, `description` 處理）


## 推薦方法

### 方法1-1：使用 Surprise 套件 -- SVD
參照 Sample code，使用 Surprise 套件中 mf-based 的 SVD 演算法 

### 方法1-2：使用 Surprise 套件 -- SVDpp
參照 Sample code，使用 Surprise 套件中 mf-based 的 SVDpp 演算法 

### 方法1-3：使用 Surprise 套件 -- NMF
參照 Sample code，使用 Surprise 套件中 mf-based 的 NMF 演算法 

### 方法2-1：使用 LightFM 套件
參照 Sample code，使用 LightFM 套件實現推薦，使用 `warp` 作為損失函數來訓練模型

### 方法2-2：使用 LightFM 套件 + item_features
加入商品資料中的四個欄位作為 item_features：
1. `price_cat`: 處理後並切分成五個等級的價格
2. `brand_clean`: 處理過後的品牌名稱
3. `rank_num_cat`: 處理後並切分成五個等級的排名
4. `title_clean`: 處理過後的商品名稱
但結果得分數並沒有提升，和方法2-1相同。

## 最終推薦分數  

成功推薦給使用者 k 個商品，最終以**方法2-1 & 2-2: 使用 LightFM model**得到最高的分數：0.083051，但還是遠低於 week1 rule-based 的成績 (0.1576) ，推測由於此資料集的使用者購買次數不夠多，商品資料也不夠完全（用來做 item_features 的這些變數並不完整，缺失值很多），因此能夠預測出的購買行為很有限，以這個情形來看，以 rule-based 產生的熱銷品來做推薦會有較好的結果。 

五個方法的分數分別如下表：  
方法 | 分數 | 執行時間
--- | --- | ---
方法 1-1: Surprise - SVD | 0 | 4min 1s
方法 1-2: Surprise - SVDpp | 0 | 1h 10min 41s
方法 1-3: Surprise - NMF | 0 | 4min 35s
方法 2-1: LightFM | 0.083051 | 10.3 s
方法 2-1: LightFM + item_features | 0.083051 | 14.2 s
