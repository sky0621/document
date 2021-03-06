# AWS負荷試験

## 負荷試験の目的

１．各種ユースケースを想定し、それぞれにおけるシステムの応答性能を推測する

２．高負荷時におけるシステムの性能改善を行う

３．目的の性能を提供するために必要なハードウェアをあらかじめ選定する (※オンプレミス環境特有)

４．★システムがスケール性を持つことを確認する

５．★システムのスケール特性を把握する

★＝クラウドならでは

## 負荷試験におけるシステムの性能指標

### ■スループット

単位時間に処理を行う量のこと。WebシステムではHTTPリクエスト数（rps）

### ■レイテンシ

処理時間（応答時間）のこと。Webシステムの場合、「ユーザーから見た処理時間」と「システムから見た処理時間」の２種類ある。

## 良い負荷試験

・試験対象システムに負荷が集中している状態

・ボトルネック部分を特定できている状態

## 負荷試験のツール

### ■攻撃ツール

システムに負荷をかけるツール。Apache Bench、Apache JMeter、Locust、Tsung等。

※なるべく試験対象システムに近い場所で用いる。（同一サーバないし拠点の近いEC2インスタンス）

### ■モニタリングツール

システムのリソース状況を観測し可視化するツール。topコマンド、netstatコマンド、CloudWatch等。

### ■プロファイリングツール

ミドルウェアやアプリケーション内部の挙動について解析し可視化するツール。Xhprof、New Relic等。

## PDCA

・目標設定

・試験実施/ボトルネック特定

・ボトルネック原因分析

・システム改善作業

## 段取り

### ■ステップ1：ツールや環境の検証

　ネットワーク、DB、外部サービスへの依存要素を極力排除した状態で攻撃ツール自体が対象サービスにどれだけの負荷をかけうるかを確認

　※対象サービスにできるだけ近い位置(対象サービスと同じサーバ内も許容)で負荷をかける

### ■ステップ2：Webフレームワークの検証

　-

### ■ステップ3：参照系の性能検証
### ■ステップ4：更新系の性能検証

　RDSアクセスが対象

 運用相当のデータ投入

　コネクションプール、インデックス、キャッシュ

　同一レコードにアクセス集中しないよう注意

### ■ステップ5：外部サービス連携の性能検証

　他サービスやS3、DynamoDB、ElastiCache等への通信が対象だが、対象との間のネットワークを中心に試験を行うため、対象へ負荷をかけることができない場合はスタブも可

　(対象単体で目標スループットを上回っていることが前提)

### ■ステップ6：シナリオ試験

　対象サーバは1台でlocalhostないし同一セグメント内サーバから攻撃する点はステップ5までと同じ

### ■ステップ7：スケールアップ/アウト試験準備

　対象サーバ1台にロードバランサー経由で攻撃

### ■ステップ8：スケールアップ/アウト試験(ステップ1～6の回帰試験)

　ステップ7までで特定されたボトルネックがスケールアップ/アウトでパフォーマンス改善されることを確認

　チューニングのたびにボトルネックリソースが変わっていく想定(始めはEC2のCPUリソース、次はRDS等)

　まずはスケールアウト、それで駄目ならスケールアップ

### ■ステップ9：限界性能試験(ステップ1～6の回帰試験)

　攻撃ツールをスケールして攻撃

## 計画

### ・スケジュール

　[メモ]

　　・数ヶ月プロジェクト規模で、負荷試験の経験が豊富で、かつ、システムの構成変更権限を持つ人の場合で1～2週間(そうでない場合は数倍かかる)

### ・負荷試験の目的

#### ・前提条件の整理

　・試験対象とするシステムの範囲

　・データ量

　・外部システムのスループットやレイテンシ、利用するシステムの制約

　　[メモ]

　　　・結合困難時はダミーサーバを立てるのか、モックにするのかも記載

### ・目標値

　・通常は「スループット」と「レイテンシ」

　・スループット目標値は「1日の利用者数」「1人あたりの1日の平均アクセス数」「1日の平均アクセス数に対する最大ピーク時の倍率」を元に決定する

　　[メモ]

　　　・最大rpsに安全係数として2～3倍かけた数字を目標とする

　・レイテンシの目標値

　　[メモ]

　　　・通常のWebシステムなら、50～100ms

### ・利用する負荷試験ツール

　・攻撃ツール

　　・手軽‥ApacheBench、JMeter

　　・複雑シナリオ可‥JMeter、Locust

　　・高負荷可‥tsung、JMeter、Locust

### ・試験実施環境

　・商用環境と同じ構成が理想

　・商用環境と大きく異なると、以下ができず無意味となることが多い

　　・可用性やスケール性の確認

　　・ボトルネックの洗い出し

　　・各種レポートの妥当な計測

　・ダミー(スタブ)サーバとモックのどちらかを使う必要がある場合はダミーサーバ(実際の処理時間Sleepも入れる)構築を優先する！(高負荷時の通信コストも見越す必要があるため)

### ・負荷試験シナリオ

　シナリオに含まれるべきリクエストの種類

　　・アクセス頻度の高いページ(機能)

　　・サーバリソース消費量が高いページ(機能)

　　・DB参照のあるページ(機能)

　　・DB更新のあるページ(機能)

　　・外部システムと通信するページ(機能)

## 負荷試験結果の最終確認

・試験対象システムに負荷が集中したか

・ボトルネック部分を特定できたか

・システムは正常にスケールしたか

・スケール特性を把握できたか

上記すべて確認できてから、

・目標値と前提条件をクリアしたかを確認

## ■準備

### ・構築

　・ELBを使う場合も直接のリクエストを許可しておく

　・アクセスログに実行時間を追加しておく

　・(できれば)HTTPでのアクセスを許可しておく

　・外部システムとの結合ポイントのオンオフができるよう、スタブを用意しておく

### ・負荷試験専用のエンドポイント追加

　・静的コンテンツだけを返すもの

　・Webフレームワークを介してHello Worldページを返すもの

### ・攻撃サーバ構築と攻撃ツールインストール

### ・シナリオ作成

### ・連携先システムとの調整

### ・AWSへの制限緩和申請

## 備考

・ELBの暖気運転

・バースト・・・T2インスタンスは貯めておいた性能を負荷増大時に解放（＝バースト）

・冗長性に関しては商用環境とまったく同じ構成にする

・CPU使用率100%はスケールアウトで解消しやすいので、そのまま進める
