# TopicModel-LDA-  

## 使用技術  
・Python  
・LDA  
・日本語評価極性辞書  
  (https://www.cl.ecei.tohoku.ac.jp/Open_Resources-Japanese_Sentiment_Polarity_Dictionary.html)  

## 概要  
商品レビューページから文章データをスクレイピング．得られたデータにトピックモデルを適用し，共通の話題ごとにグループ化を行う．  
文章データにトピックモデルを適用するにあたり，適用可能な形に文章を整形する必要がある．  
例えば，「サッカー」と「Soccer」はどちらも同じ意味を持つが表記の違いにより異なる単語として分類されることがある．  
文章の分類にいおいては確立分布的に単語の頻出度を算出し，もっともらしい分類を行うため予め文章データに前処理を行うことで極力文章の標準化を図る．  
文章を分類した後，同じグループ内の文章データに対し日本語評価極性辞書を用いたポジティブ/ネガティブ度合いをスコアリングし，レビューに潜むユーザーの感情や満足感を数値化．  
算出されたスコアを比較評価することでデータに潜むユニークなアイデアを定量的に抽出することを目指した．  

以下の流れでシステムを実行  
・商品レビュー記事から文章データのスクレイピング  
・文章データの前処理(標準化)  
・トピックモデル適用にあたり，分類数(トピック数)の最適解を算出  
・トピックモデルを用いて文章の分類化  
・1つのグループに着目し，グループ内の文章データに日本語評価極性辞書を用いたポジティブ/ネガティブ度合いを数値化  
・文章間でスコアを比較することで1つのグループ内に潜むユニークなアイデア（他とは異なる考えや満足感）を抽出  


## 構成  
-Topicフォルダ  
  class_topic.py  :  文章データの整形とトピックモデルの適用  
  topic_num.py    :  トピックモデルを適用するにあたり，トピック数の決定  
  wcloud.py       :  トピックモデル適用後の分類結果の可視化  
  
-kyokuseiフォルダ  
  kyokusei_class.py : 文章データにポジティブ/ネガティブ判定ベースのスコアリング  
  kyokusei_class    : 文章データにスコアリングをしたのち，データを保存  
  kyokusei_hist     : 文章データのスコアリング結果をヒストグラムにして表示  
  
  *本研究の一部のみ掲載しています．  

## 文章データの整形  
トピックモデルに適用するにあたり，分類の精度を向上させるためweb上の文章データ（商品レビュー）に以下の前処理を実行．  
・表記揺れの削除  
・大文字小文字等の統一化  
・ストップワード(文章の話題特定に意味をなさないもの:助詞や副詞など)  
・インターネット新語対応の辞書作成  
・絵文字，記号の削除  
・形態素解析を行い，文章を単語ごとに分割  


## Topic数の決定  
整形した文章データを分類するにあたり，予め分類数（Topic数）を決定する．  
2つのパラメータを用いて計算することにより，最適解を導き出し決定可能．  
下記のケーススタディでは赤枠のTopic数10が最適解として算出．
![topic_num](https://user-images.githubusercontent.com/77096897/151784019-5eaeff2d-e07f-470d-a103-c1fbacaef4a8.png)  


## Topic Model の適用と文章データの分類  
文章データにトピックモデルを適用．トピックモデルの中でもLDA(Latent Dirichlet Allocation)を用いた．  
先ほど決定したTopic数で分類し，WordCloudを用いて結果を可視化．(下記図)  
1つのトピック内で，単語の頻出度合いが文字の大きさに比例して表示される．  
例えば，
![wordcloud](https://user-images.githubusercontent.com/77096897/151784646-069301f7-95aa-40a2-8f3d-d649e200281e.jpg)　　


## 同グループ内で文章の潜在的な感情・満足感の数値化  
Topicモデルを用いて分類化した文章データに日本語評価極性辞書を用いて作成したポジティブ/ネガティブ判定を行う．  
単語ごとにスコアリングを行い，文章ごとにそのスコアを集計する．同一グループ内で点数の評価・比較を行う．  
得られた結果の中で他のデータとは異なるスコアを持つデータをユニークなアイデアの候補として抽出する．  
