# data-course-sample

# Week2
## 專案目的

使用「使用者購買商品紀錄」以及「商品基本資訊」，以內容過濾 (Content-based filtering) 的方式，為指定的使用者推薦 k 個其可能有興趣的商品，並以使用者**是否實際購買推薦商品**作為評價的指標。

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

## 資料清洗

商品基本資訊 `meta_All_Beauty.json`：

- 空值及商品基本資訊重複的處理
- 銷售排行 `rank` 的處理，其中移除了非屬 Beauty & Personal Care 類別的商品 （數量僅佔全部商品的大約 1.5%)
- 價格 `price` 數值處理

購買商品的評論紀錄 `All_beauty.csv`:

- 和商品基本資訊相同，移除非屬 Beauty & Personal Care 類別的商品 （數量僅佔約 2%)
- 針對 `title` & `description` 的文字部分進行清理

## 資料探索與思考邏輯

- 商品基本資訊中有紀錄相關商品資訊，包含 `also_buy`, `also_view`, `brand`, `title`, `description`, `price`, `rank` ，但部分欄位資料不足，如：
    - `brand`：92.5% 的品牌都只擁有 < 5 個商品，且僅約一半 (51.93%) 商品有品牌資料
    - `price`：僅 34.42% 商品有價格資料
    - `also_view`：僅 24.69% 商品有 `also_view`資料
    - `also_buy`：僅 20.32% 商品有 `also_buy` 資料

- 由於 `description` 欄位資料過大，故最終未將其納入特徵


## 推薦規則

### 規則一：TFIDF (Item Title) & Cosine Similarity
使用商品 title 
使用 TFIDF 將商品 title 轉換成向量，再以 Cosine similarity 計算商品間的相似度


### 規則二：TFIDF (Item Title) & Euclidean Distance
使用 TFIDF 將商品 title 轉換成向量，再以 Euclidean Distance 計算商品間的相似度


### 規則三：TFIDF (Item Title + Brand) & Cosine Similarity
使用 TFIDF 將商品 title 及 brand 分別轉換成向量並相乘，再以 Cosine similarity 計算商品間的相似度


### 規則四：Sentence Transformer (Item Title) & Cosine Similarity
使用 Sentence transformer 的 pretrained model: `all-MiniLM-L6-v2` 將商品 title 轉成向量，再以 Cosine similarity 計算商品間的相似度




## 最終推薦分數

成功推薦給使用者 k 個商品，最終以**規則1: 使用 TFIDF & Cosine Similarity**得到最高的分數：0.0051
另外，若結合 week1 的 rule-based 推薦方式，得到和 week1 分數相同 (0.1576) 的結果，表示此 content-based 並未有效推薦商品，使用 rule-based 的熱銷品推薦在這個場景已經足夠。


四個規則的分數分別如下表：  
規則 | 分數
--- | ---
規則1 | 0.0051
規則2 | 0
規則3 | 0
規則4 | 0.0034
