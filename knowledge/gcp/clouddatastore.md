# Cloud Datastore

## 概要

- 「分散キーバリューストア」ないし「NoSQL」

- スケールアウト型（複数のサーバにデータを分散保存）のデータストアでありながらストロングコンシステンシーを持った検索が可能

- 保存可能なエンティティ数は実質無制限

- Kind（＝Excelの１シート、RDBの１テーブルに相当）は、各行を特定する

## コンシステンシー

### グローバルクエリ―

「イベンチュアル・コンシステンシー」・・・結果整合性（＝書き込みデータが即座には反映されない）

### Ancestorクエリー

「ストロング・コンシステンシー」・・・強い整合性
