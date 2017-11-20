# Babel

## インストール

npm install --global babel-cli babel-preset-es2015

## 変換

babel xxxx.js --presets=es2015 -o yyyy.js

## .babelrc

<pre>
{ "presets": ["es2015"] }
</pre>

## 変更を監視

babel xxxx.js -w -o yyyy.js

## ディレクトリ内を一気に変換

babel src -d dest

## コンパクト化

babel src --compact=true -d dest

## ソースマップ

babel xxxx.js -o yyyy.js --source-maps
