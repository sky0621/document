## CAP定理

### 一貫性(Consistency)

全てのデータ読み込みは、最新の書き込みデータ、またはエラーのどちらかを受け取る。

### 可用性(Availability)

単一障害点が存在しない。
(ノード障害が起きても、ダウンしていないノードが応答を返す。)

### 分断耐性(Partition-tolerance)

通信障害によるメッセージ損失が起きても、システムは処理を継続できる。
(通信可能なサーバが複数のネットワークに分断されるケース)

### セオリー

・タイムアウトが発生した時に「(一貫性が得られるまで)処理を中断して可用性を犠牲にする」のか「処理を進めて一貫性を犠牲(一時的に古い情報を参照しうる)にする」のか

## ACID vs BASE

### ACID

・原子性(Atomicity)

・一貫性(Consistency)

・独立性(Isolation)

・永続性(Durability)

### BASE

Basically Available, Soft-state, Eventual consistency
