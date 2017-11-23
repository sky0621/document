# Dep

## リポジトリ

https://github.com/golang/dep

## 概要

go純正の（glideに代わる）依存モジュール管理ツール。

## 注意点

Go 1.8 以降でないと使えない。

## 使用メモ

### glideからの移行

<pre>
dep init
</pre>

glide.lockでの使用Verも検知して依存ファイルを作成してくれる。
