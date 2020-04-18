---
layout: default
title: 相関係数を用いた為替レート予測
---

2019-04-18

過去のFXチャートと似た値動きがあれば、将来のレートも過去と同様の動きになるのではないかという仮説について検証を行った。

### 仮説

将来の為替レートが上がるか下がるか予測したい。過去のチャートと似た値動きがあれば、将来のレートも過去と同様の値動きになるのではないか。

例えば、T日～当日までの値動きが初めは下がって後に上がるようなチャートであった場合、N日後のレートは過去にも同様のチャートがあるか調べ、あった場合は将来のレートも同様の動きをする可能性があると考えられる。

下図の赤枠のように同様のチャートになるケースが多いのではないか。

![20190730210529](https://user-images.githubusercontent.com/48476117/79638734-125dd800-81c2-11ea-8ee9-fc3952365b7c.png)

### 準備

#### データ

2002年4月1日～2019年4月16日までのみずほ銀行の外国為替公示相場レートを使用した。第二公示が出された日もあったが第一公示のみを使用(それに加工しづらいデータだった)。

[ヒストリカルデータ | みずほ銀行](https://www.mizuhobank.co.jp/market/historical.html)

#### 学習データとテストデータ

- 2002年4月1日～2018年12月最終営業日まで: 学習データとして似たチャートを探すための過去データとする。
- 2019年1月第1営業日～2019年4月16日まで: テストデータとして予測精度の評価に使用する。

#### 環境

[Google Colaboratory](https://colab.research.google.com/)

#### 検証プログラム

[GitHub](https://github.com/ta9t2/)

### 検証方法

#### 検証(予測)対象の通貨ペア

USD/JPY

#### 比較対象の過去レート

USD/JPYに加えて、GBP/JPY、EUR/JPYも対象とする。FX市場全体でのJPYの強弱が将来のレート変動に影響すると考えているため、それを加味できるようにメジャーな3通貨との円貨を比較する。

![20190730210642](https://user-images.githubusercontent.com/48476117/79638754-20abf400-81c2-11ea-81ec-f4be90fc432a.png)

#### 似たチャートの探し方と将来レートの予測方法

1. T日～当日までのUSD/JPY、GBP/JPY、EUR/JPYのいずれのチャートに対しても相関係数7.0以上の過去のチャートを似た値動きをするチャートと判断する。
1. 見つかったチャートに対してN日後のレートとTODレートの割合を求める(N日後レート / TODレート)。1.0以上なら将来レートは上がる、以下なら下がると予測する。
1. 複数のチャートが見つかった場合は期待値を使用する。

#### 評価基準

- USD/JPYのN日後のレートが予測通り上昇した、もしくは下降した場合を正答とする。
- 予測と逆のケースは誤答とする。
- 相関係数の高いチャートがなく、予測できなかったケースは除外する。
- 正答率が高ければ仮説は正しいと考えることにする。

#### T日とN日(説明変数)の決め方

上述の評価基準で正答率が最大となるT日とN日を設定する。設定値は下表の通り。

尚、計算リソースの関係で算出範囲を限定した。この範囲の計算でも6時間以上を要した。

| 説明変数 | 設定値 | 定義 | 算出範囲 |
|-----------|-----------|-----------|-----------|
| T | 34日 | 比較するチャートの期間(T日から当日までの期間のチャートについて相関係数を求める。) | 20日～60日 |
| N | 7日 | 予測日(当日からN日後のレートを予測対象とする。) | 1日～10日 |

### 結果

下表の通り正答率が70%を上回る結果になった。

損益はレートが将来上昇すると予測した場合に100通貨買い、N日後に売った想定で算出した。空売りはなし。レバレッジは等倍。スプレッドは考慮しない。

| 正答率 | 正答計 | 正答:上昇 | 正答:下降 | 誤答計 | 該当なし | 全体 | 損益 |
|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|
| 74.40% | 29件 | 18件 | 11件 | 10件 | 24件 | 63件 | 1300円 |

![20190730210717](https://user-images.githubusercontent.com/48476117/79638787-4e913880-81c2-11ea-9a2c-716a719aa9c2.png)

### 評価と考察(主に感想)

正答率は高かったが、検証期間が短く実用レベルとは言えないと思う。結果的には損益もプラスになっているが、上がるか下がるかの2択の予測しかしていないため、一回の誤答で大幅なマイナスになる可能性がある。また、今回の評価期間はたまたま上昇トレンド(USD安、JPY高の傾向)であったためUSD買いで損失を出す可能性も低かった。

#### レートの変動率予測と相関係数の散布図

1.0(変動0)を中心にけっこう綺麗に散らばっていた(下部の方にへんな偏りがあるが)。レートは満遍なく上がったり下がったりしているんだなと思った。主要通貨のレートなので当然のことなのかもしれないけど。

![20190730210743](https://user-images.githubusercontent.com/48476117/79638793-5ea91800-81c2-11ea-918b-8a9503262b43.png)

#### 正答率の偏り

##### 正答率とT日の関係

T日は比較するチャートの期間になるので、その長さで正答率の偏りをみてみた。期間が長くなればなるほど正答率も上がるかと思ったが、そうとは限らないようだ。今回のデータでは。

![20190730210810](https://user-images.githubusercontent.com/48476117/79638802-6a94da00-81c2-11ea-941d-b4f230b0d9a3.png)

##### 正答率とN日の関係

N日は当日から予測日の差になる。6営業日前後の正答率が比較的に高い結果となった。

![20190730210833](https://user-images.githubusercontent.com/48476117/79638809-76809c00-81c2-11ea-8dd3-4a658fb7458c.png)

#### プロフィットファクターの観点

そもそも為替トレードでは勝率よりも損益(プロフィットファクター)に着目して評価する必要があったのではないか。

プロフィットファクターに着目した場合、TとNの最適値はそれぞれ下表の通りで、損益は下図のようになる。正答率は60%程度で、損益は1797円になる。但し、10日先のレートを予測するため損益の最終評価日は正答率重視の場合と異なる。

| 説明変数 | 設定値 |
|-----------|-----------|
| T | 53日 |
| N | 10日 |

![20190730210906](https://user-images.githubusercontent.com/48476117/79638812-80a29a80-81c2-11ea-9de4-4ac0b3d424dc.png)

#### 精度向上の観点

- 米雇用統計発表等のイベントに影響された期間のデータは除外した方がより精度が高まったかもしれない。
- T日とN日は今回のテスト期間に最適化されてしまっているため、期間を広げて算出した方がより精度が高くなるものと想定している。しかし、算出に時間を要するためそのリソースを掛けてまで求める価値があるかは疑問だ。
- 今回の検証では過去データの全期間から単純に相関係数を用いて近似したチャートを探したが、トレンドごとにデータを区切り、T日とN日を設定した方がより精度が高まるかもしれない。

### その他(備忘など)

- Google Colaboratoryという面白い環境(準備不要のJupyter Notebookという感じでとても使いやすかった)を見つけたので、NumPyなどの機械学習でよく使いそうな基本的なライブラリの実装練習を兼ねて作成した。
- 探せば同様の観点で検証している記事はありそうだが、ざっと探した感じでは見つからなかった。
- こうやって個人で仮説を立ててモデルを構築していくことは趣味としては楽しかった。しかしFX取引の運用として実用的なレベルに達するのは難しいと感じる。学習コストを考えればMetaTraderの自動売買スクリプトを買った方が手っ取り早い気がする。
