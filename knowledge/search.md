# 幅優先探索

最短経路問題を解くためのアルゴリズム

## 手順

### 1. 問題をグラフとしてモデル化

#### ■グラフの構成要素

- ノード(node)

- 辺(edge)

※ノードとノードが直接接続していることを「隣接(neighbor)」という

#### ■無向グラフ

[taro] - [hanako]

二点間の関係。taroはhanakoを知っており、hanakoはtaroを知っている。

#### ■有向グラフ

[taro] -> [hanako]

hanakoはtaroを知らない。

### 2. 幅優先探索を使って問題を解く

1. 開始ノードの隣接ノードに目的の品が存在するか、全ての隣接ノードを探索

<forループ>

　2-1. 目的の品が見つかれば終了

　2-2. 探索中ノードの全ての隣接ノードを探索

　<forループ>

　　3-1. 目的の品が見つかれば終了

　　3-2. 探索中ノードの全ての隣接ノードを探索

　　　　…以下繰り返す

#### ■要点

開始ノードに近い(隣接している)ノードから探索

探索リストはキュー(FIFO＝最初に追加されたものから順に処理)を想定

## 時間計算量

探索には辺を辿る必要があるため、少なくとも O(辺の個数) の計算時間がかかる。

また、検索対象のキューに1要素を追加するのにかかる時間は O(1) のため、全要素分追加すると O(要素数) となる。

あわせて、O(追加する要素数＋辺の個数) となる。

## トポロジカルソート

タスクAがタスクBに依存する場合、タスクAはタスクBより後ろにあると言える。

このような、グラフから順序付きリストを作成する方法をトポロジカルソートと呼ぶ。

例：結婚式の計画立て

## ツリー

家系図など、辺が一方通行の矢印の場合、特殊なグラフとして、木またはツリーと呼ぶ。

