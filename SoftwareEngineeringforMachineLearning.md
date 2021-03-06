---
title: 【論文紹介】機械学習するときのベストプラクティスをマイクロソフトさんが教えてくれるらしい
tags: ソフトウェア工学 機械学習 論文読み
author: pn8128
slide: false
---
こんにちは、美味しいしです。

IEEE/ACM 2019で発表されたソフトウェア工学の論文「[Software Engineering for Machine Learning: A Case Study](https://ieeexplore.ieee.org/abstract/document/8804457)」を読んだので、まとめます。

**本記事の流れ**:

- **0.忙しい方へ**
- **1.機械学習を用いた開発を取り巻く背景**
- **2.機械学習を用いる際のベストプラクティス**
- **3.機械学習を用いた開発の習熟度をスコア化**
- **4.今後に向けた議論**
- **5.まとめと所感**
- **参考**


## 0. 忙しい方へ

- **ベストプラクティスを収集したよ**
    - ソフトウェアエンジニアの日々のワークフローを統一し、自動化することが重要
    - 各データセットにどこから収集したか、抽出にどのバージョンのコードが使われたのか（および関連する特徴）の情報をタグ付けすると良い
    - 機械学習の専門家でなくても機械学習を学ぶ機会が大事
    - モデルの限界を知るために、より解釈可能なモデルを使用することや、ブラックボックスモデルをより解釈可能にする可視化技術を開発することが必要
    - テストの自動化は重要であるが、必要に応じて人間がエラーを見て問題の原因を特定するために仮説を立てることも依然として必要である。
    - エンジニアが重要だと感じる問題の種類はAIを使って経験を積むにつれて変化する
- **機械学習における組織のソフトウェアプロセスを評価する指標を作ったよ**
    - 目標が定義されているか
    2. 一貫して実装されているか
    3. ドキュメントが整備されているか
    4. 自動化されているか
    5. 測定・追跡されているか
    6. 継続的に改善されているか
- **普通の開発とMLは以下の3つの点で大きく異なるよ**
    - MLではデータの発見、管理が複雑で困難
    - MLのモデルはカスタマイズや再利用の方法が既存の開発と違う
    - 個別のモジュールとして扱うことが難しい(モデルが複雑に「絡み合って」いる可能性があるため、面倒なエラー動作に直面することがある。


## 1 機械学習を用いた開発を取り巻く背景
### 1.1 機械学習ワークフロー
機械学習を用いた開発をする際には、以下9つのステップがある。(以下図1とする。)
![9つのステップ](./img/SoftwareEngineeringforMachineLearning.png)

#### Model Requirements
設計者が機械学習で実装可能な機能と製品に有用な機能を決定するステップ。
この段階で、解決すべき課題に対して**どのようなタイプのモデル**が最も適切であるかを決定する。
#### Data Collection
既存の利用可能なデータセットを探して統合するか、独自のデータセットを収集し、データを集めるステップ。多くの場合、既存のデータセットを使用して部分的なモデルを訓練した後、独自のデータと一緒に転移学習を使用して課題に沿ったモデルを訓練する。
#### Data Cleaning
データセットから不正確なレコードやノイズの多いレコードを削除するステップ。
#### Data Labeling
データにラベル付けをするステップ。
オンラインクラウドソーシングプラットフォームのクラウドワーカーに外注することもある。
#### Feature Engineering
機械学習モデルのための有益な特徴量を抽出するステップ。
畳み込みニューラルネットワークなどでは、次のモデルトレーニングに組み込まれていることが多い。
#### Model Training
モデルをトレーニングするステップ。
#### Model Evaluation
事前に定義された指標を使用して、テスト済みまたは保護されたデータセット上で訓練されたモデルを評価するステップ。人手による評価が必要になることもある。
#### Model Deployment
モデルをデプロイするステップ。
#### Model Monitoring
実世界での実行中にエラーが発生する可能性があるかどうかを継続的に監視するステップ。

図では一直線にステップが並んでいるが、しばしばフィードバックループが生じる。

### 1.2 機械学習のためのソフトウェアエンジニアリング
近年，隠れた技術的負債や統合型AIのトラブルシューティングなどの文脈で、ソフトウェア工学的手法を調整する必要性が議論されており、作業プロセスを自動化しようとする試みがたびたび回行われてきた。しかしながら、作業プロセスを汎用化し、標準化するのに苦労していることが示されている。

### 1.3 プロセスの成熟度

ソフトウェアエンジニアの能力の習熟度は、その領域での経験に応じて多岐にわたっていそ。
ソフトウェア工学では、ソフトウェアプロセスの改善のために、能力成熟度モデル(CMM) といった組織のソフトウェアプロセスを評価する指標があるが、機械学習における成熟度モデルは本研究以前には存在しなかった。

## 2 機械学習を用いる際のベストプラクティス
マイクロソフトの4195名に社内アンケートを送付して得た、551名のソフトウェアエンジニアからの回答(回答率13.6%)を分析することにより以下の知見を得た。

### 2.1 エンドツーエンドのパイプライン支援
機械学習コンポーネントの成熟・大規模に伴い、従来のソフトウェア開発基盤にML開発支援を統合することが重要になってきた。図１のすべてのステージを可能な限りシームレスにカバーする開発が行えることが、自動化にとって重要であるとわかった。つまり、**ソフトウェアエンジニアの日々のワークフローを統一し、自動化することが重要である。**
課題としては、データ駆動型学習アルゴリズムの固有の不確実性と、隠れたフィードバックループによって引き起こされる複雑なコンポーネントのもつれにより従来のソフトウェア工学的な手法が使えないことがあることが挙げられる。
エンジニアがデータの発見、収集、インジェスト、理解、変換を行い、モデルのトレーニング、デプロイ、保守を行うのを支援するために、「エンジニアが手間をかけずにハイパーパラメータの異なるAIアルゴリズムの多くの組み合わせを試すことができるように、データの継続的なロード・変更が可能な、強固なデータパイプライン」の開発が必要である。
なお、視覚的なツールは初心者にとっては有用であるが、経験を積むにつれ別のツールを好むようである。

### 2.2 データの収集、清掃、管理

多くの機械学習技術は大規模なデータセットからの学習を中心としているため、ML中心のプロジェクトの成功は、データの可用性、品質、管理に大きく依存することが多い。データセットのラベル付けにはコストと時間がかかるため、(コンプライアンス上の制約が許す限り）データを可能な限り再利用することが重要である。
可用性に加え、以下のようなデータ属性が重要視されている。
(アクセシビリティ、正確性、信用性、鮮度、遅延、構造性、関連付け、接続性、セマンティック結合性)
また、データの収集・前処理・管理を自動化することも重要だ。自動化することで "新しいモデルを実験しながら、実験のスピードを上げ、ライブデータを使って作業する "ことができるようになる。
データとモデルの管理活動の断片化を避けるためには、データ管理ツールとMLフレームワークの統合が必要である。ここで重要になってくるのが、機械学習のデータソースは急速な変化するという点である。データの継続的な変化は、エンジニア自身が開始した操作と入ってくる新しいデータといった2つの場合があるが、どちらにおいても厳密なデータのバージョン管理と共有技術が必要になる。データセットを配備されたモデルにマッピングしたり、データの共有や再利用を容易にするためには、**各データセットにどこから収集したか、抽出にどのバージョンのコードが使われたのか（および関連する特徴）の情報をタグ付け**すると良い。

### 2.3 教育と訓練
機械学習の用途は急速に広がりを見せ、現在ではソフトウェアから組み込み機器まで多岐にわたる。そのため、たとえ機械学習を専門としてきたエンジニアでなくても機械学習の専門家と一緒に仕事をする方法を学ぶ必要がある。機械学習とデータサイエンスに関する社内会議の開催(マイクロソフトは年1回)や機械学習やディープラーニングに関するオープンフォーラム(マイクロソフトは毎週)などが有用だとされる。またこれらのフォーラムでは、誰でもAIや機械学習に関する技術的な質問や実用的な質問に答えたり、学会で発表された最近の成果を共有することが必要である。

### 2.4 モデルのデバッグと解釈可能性
データから学習するコンポーネントのデバッグ活動は、プログラミングのバグだけでなく、モデルのエラーや不確実性から生じる固有の問題にも焦点を当てている。モデルの限界を知るために、より解釈可能なモデルを使用することや、ブラックボックスモデルをより解釈可能にする可視化技術を開発することを提案している。大規模なマルチモデル・システムでは、エラー解析とデバッグ性を単純化するために、従来の階層型のソフトウェア・アーキテクチャを用いてモジュール化が行われている。
### 2.5 モデルの進化、評価、展開
機械学習を中心としたソフトウェアは、モデルの変更・パラメータの調整・データの更新などにより、頻繁にバージョンアップされ、それらの組み合わせがシステムの性能に大きな影響を与える。これらの変更が与える影響を評価するために、コンボフライト（変更と更新を組み合わせのテスト）が用いられている。これは、複数の評価指標を含め、より機密性の高いデータカテゴリについては人間主導の評価を行う、体系的なプロセスである。テストの自動化は重要であるが、必要に応じて人間がエラーを見て問題の原因を特定するために仮説を立てることも依然として必要である。
システムのデプロイをスムーズに行うためには、トレーニングとデプロイのパイプラインを自動化するだけでなく、 モデル構築を他のソフトウェアと統合し、MLと非MLの両方のコードベースで共通のバージョニング・レポジトリを使用し、MLと非MLの開発スプリントと立ち上げを緊密に連携させることが推奨されている。

### 2.5 経験などによる見解のバラつき
個人のAI経験、チームのAI経験、全体的な仕事の経験、同時進行のAIプロジェクトの数、回答者が機械学習やデータサイエンス技術の正式な教育を受けていたかどうかを説明変数としたモデルを構築するためにロジスティック回帰分析を実施しました。その結果、5つの有意な係数が見出された。

- 教育と訓練は、個人的なAI経験と負の相関があり、係数は-0.18 (p < 0.02)である。
	- **AI経験の少ない人にとって教育と訓練がより重要な問題である。**
- 他者を教育することは、個人的なAI経験と正の相関があり、係数は0.26(p < 0.01)である。
	- **AI経験の多い人は他者を教育することがより重要な課題である。**
- ツールの問題は、チームのAI経験と正の相関があり、係数は0.13(p < 0.001)である。
	- **チームがAIプロジェクトでの作業経験を積むにつれて、他の人や自分のツールに頼る度合いが高くなり、それらのツールが与える影響について考える頻度が高くなることを意味している。**
- エンドツーエンドのパイプライン支援は、正式な教育と正の相関がある（p < 0.01）。
	- **正式な教育を受けた人だけがエンドツーエンドのパイプラインの構築に取り組んでいることが示唆される。**
- 仕様書と正式な教育と正の相関がある（p < 0.03）。
	- **正式な教育を受けた人がモデルやエンジニアリングシステムの仕様書を作成していることが示唆される。**

これらの分析から、**エンジニアが重要だと感じる問題の種類はAIを使って経験を積むにつれて変化する**ということがわかる。いくつかの問題は一過性のもので、チーム内での自分の立場や、一緒に作業することの偶発的な複雑さに関連しています。一方で、機械学習をソフトウェアアプリケーションに統合する実践にとってより根本的で、経験レベルに関係なく多くのエンジニアに影響を与える問題もある。

## 3 機械学習を用いる際のベストプラクティス

チームのMLの成熟度は、各ワークフローのステージが以下の指標を満たしているかで評価できる。

1. **目標が定義されている**
2. **一貫して実装されている**
3. **ドキュメントが整備されている**
4. **自動化されている**
5. **測定・追跡されている**
6. **継続的に改善されている**

これら6つの指標に5段階評価で回答してもらいその平均を取ることにより、アクティビティ成熟度指標（AMI）を定義した。

AMIが成熟度指数を検証する手段として適切かどうかを検証するために、チームの実践がどの程度効果的かを5段階評価で回答してもらうことにより、活動の効果（AE）を得た。AMIとAEの間のスピアマン相関は、すべてのAI活動について0.4982から0.7627の間であった（いずれもp < 0.001で統計的に有意）。これにより、成熟度指数がAI活動の成熟度と有効性を捉えることができる有効な複合指標であることを示唆された。

## 4 今後に向けた議論

以下は、ソフトウェア工学が過去の一般的なアプリケーション領域をサポートするために適応されてきた方法と、人工知能アプリケーションやプラットフォームをサポートするためにどのように適応させることができるかについての議論である。

### 4.1 データの発見と管理

既存のソフトウェア工学がコードの設計に着目していたのと同様に、機械学習にとってはモデルを特徴づける「データセット」が重要である。しかしながら、機械学習で使用されるデータは、多義的・文脈依存的であり、まとめて捉えがたく、記述するのが複雑な場合がありる。
エンジニアは、モデルのトレーニングやチューニングに使用するためのデータを探し、収集・選別・前処理をする必要がある。その際に、すべてのデータは保存、追跡、バージョン管理されねばならない。機械学習においてはデータの更新が速いため，データのスキーマ（およびデータ）は一日に何度も変更されるからである。しかしながら、データセットには、列を記述し、その統計的分布を特徴付けるための明示的なスキーマ定義がほとんどない。
この問題に対しては、以下の対策が考えられている。

- 電気工学から着想を得たデータシートを使用して、これらのデータセットのメタデータ特性をより透過的かつ確実に追跡する[1]．
- データセットを互いに比較するために、Datadiff [2] ツールを使用する。これにより開発者はデータサンプル上で実行可能な変換関数を定式化することができる。

### 4.2 カスタマイズと再利用

訓練済みのMLモデルは、与えられた入力に対して呼び出すことができる関数のように見えるが、実際はもっと複雑である。
モデルは、アルゴリズムとパラメータのセットに分けられる。パラメータのセットは訓練中に学習されるが、このパラメータセットが人が単純に変更できるものでないことが問題である。
異なるドメインでモデルを実行したり、わずかに異なる入力フォーマットを使用したりする必要がある場合には、パラメータを変更する必要がある。この際、モデルの再訓練や、最悪の場合、モデルの置き換えが必要となる。どちらにおいても、追加の学習データを発見し、収集し、洗浄する必要があり、元のモデルの作者が費やしたのと同じくらいの労力と専門知識が必要となる。

### 4.3 MLのモジュール性

機械学習モデル間の厳密なモジュール境界を維持することは、以下2つの理由から困難である。
1. モデルの拡張が困難である点
2. モデルが互いに非明示的に相互作用する点
	- 特に複数のモデルを用いる際には、それぞれのモデルを構築したチームが互いに密接に協力する必要がある。（コンポーネント・エンタングルメント）
	- 機械学習モデルが互換性のある方法で更新される必要がある。もし非互換的にモデルが更新されてしまうと、そのエラーは大変見つけにくい。



## 5 まとめと所感

機械学習を用いる際には、従来のソフトウェア工学的な手法が通じないことが多いことがわかった。
昨今、環境を再現するツールは多くあるが、データセットの共有も含めたツールは統一されていないように思う。(githubのレポジトリなどでもパラメータなどはcurlやwgetしてねと書いてあることが多い。)これには、ツールの管理者側のハードディスク容量や通信容量の問題などがあると思われる。（最近もDocker Hubが6カ月使われていないコンテナイメージを削除するしないで話題になった。)そのうち、データセットの共有も含めた機械学習プラットフォーム共有サービスがサブスクとして現れるのではないか。(現在はまだデータセットの価値が高すぎるので一般に共有したいとはならないが、self-attentionモデルなどにより必要データセット量が減ってくればまた新しい展開もありうると考える。)
また、エンジニアとしてのステップアップのためにも仕様書を書く習慣をつけることが大切であると思う。
テストの完全自動化が困難であり、バージョン更新の際にも機械学習の知見が必要であるため、機械学習エンジニアが完全に不要になることは当面ないのではないかという印象を受けた。


## 6 参考

原著：[Amershi S, Begel A, Bird C, DeLine R, Gall H, Kamar E, Nagappan N, Nushi B, Zimmermann T. Software engineering for machine learning: A case study. In2019 IEEE/ACM 41st International Conference on Software Engineering: Software Engineering in Practice (ICSE-SEIP) 2019 May 25 (pp. 291-300). IEEE.](https://ieeexplore.ieee.org/abstract/document/8804457)

[1] [T. Gebru, J. Morgenstern, B. Vecchione, J. W. Vaughan, H. M. Wallach, H. D. III, and K. Crawford, “Datasheets for datasets,” *CoRR*, vol. abs/1803.09010, 2018.](https://arxiv.org/abs/1803.09010)

[2] [C. Sutton, T. Hobson, J. Geddes, and R. Caruana, “Data diff: Inter- pretable, executable summaries of changes in distributions for data wrangling,” in *Proc. of the 24th ACM SIGKDD*. ACM, 2018, pp. 2279–2288.](https://dl.acm.org/doi/abs/10.1145/3219819.3220057?casa_token=NhHfHrpudncAAAAA%3A9pL5cxFFXsgm518eIia0K1VPcGDTbmxqp2bG2xHdorG1ZdtNqh8q9XvijIEXn8yTJuSJ69msMuvt3w)



