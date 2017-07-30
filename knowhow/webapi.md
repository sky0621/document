# エンドポイント設計
---
## ■考え方
+ 全て小文字を使うのが基本
+ 「.php」や「.do」などサーバ側アーキテクチャが反映されたURIは避ける

## ■HTTPメソッド
### GET
+ 情報の取得を行う機能にのみ使う（＝サーバ側のリソース変更を伴う処理には使わない）

### POST
+ 指定したURIに属する新しいリソースの送信に使う（＝リソースの変更や削除には使わない）

### PUT
+ 更新したいリソースのURIそのものを指定し、その内容を書き換える機能に使う
+ 送信するデータで元のリソースを完全に上書きする（※情報の一部だけを書き換える場合は "PATCH" メソッドを使う）

### DELETE
+ リソースの削除に使う

### PATCH
+ リソースの情報すべてではなく一部だけを更新したい場合に使う

## ■エンドポイント設計
+ 名詞の複数形を使うのが基本

  + 「https://domain/getUser」×

  + 「https://domain/users」○

+ スペースやパーセントエンコーディングを要する文字は使わない

+ 単語をつなげる場合やハイフンを利用する(Googleが推奨)

  + profile-image ○

  + profile_image ×

  + profileImage ×

## ■検索とクエリパラメータ設計
+ ページネーションには「limit」と「offset」を使った相対位置指定が一般的

  + 検索ストレージの先頭から何件目より何件分を取得という方式のためパフォーマンス問題が発生
  + ページングタイミングによりデータのズレ問題も発生

+ 絞り込みパラメータが１つの場合は「q」をパラメータ名とする

  + 「https://domain/search?q=Taro」

+ 検索条件(及び、指定を省略しても成り立つもの)はクエリパラメータで指定

+ 「api.～～」

## ■認証　**★記述途中★**
---
+ APIの認可はOAuth2.0を使う
+ OAuth2.0の認可フローは４種類
  + Authorization Code = サーバサイドWebアプリ向け
  + Implicit = スマホアプリやJavaScript使用クライアントサイドアプリ向け
  + Resourse Owner Password Credentials = 別サーバサイドアプリ使わず直接サーバサイドアプリにパス認証でトークンゲット

　　★以下をPOST

　　+ grant_type="password"

　　+ username

　　+ password

　　+ scope ... アクセススコープ(省略可)

　　　※Authorizationヘッダでサービス毎の認証情報(ID/パスをBase64変換)

　　➡サーバからのレスポンス

　　+ access_token

　　+ token_type: bearer
　　+ expires_in

　　+ refresh_token

+ Client Credentials = ユーザ単位で認可しないアプリ

# レスポンス
---
+ 基本はJSON
+ リクエストヘッダ「Accept」で「application/json」と指定することを求める
+ サーバ側のストレージ内容に依存せずユースケースを考慮したデータ構造にする
+ 通信減らす方向を目指す
+ なるべくフラットな構造にする
+ トップレベル要素は配列でなくオブジェクト(JSONインジェクション対策)
+ ページネーション・・・20件表示なら21件取ってみると、21件目の有無で次ページ要否判定可能
+ JSONはキャメルケースで命名
+ 性別(生物学的)…M(男性)、F(女性)、U(不明orその他)
  + 社会的な性別(gender)の時は多様
+ 日付フォーマットはRFC3339(W3C-DTF)が標準
  + 2017-07-19T08:30:49+09:00　※タイムゾーンはUTC(+00:00またはZ)
+ エラー詳細・・・ステータスコード 500 等の際にレスポンスボディに詳細コードを含めるか、詳細ドキュメントへのリンクを含めるか、ユーザ向けと開発者向けのメッセージを分けるか検討
+ エラーの際にHTMLが返らないようにする
+ メンテナンス時は 503 とHTTPヘッダ(Retry-After: Mon, 2 Dec 2013 03:00 GMT等)
+ Dateヘッダ(HTTP時間＝RFC1123でタイムゾーンはGMTのみ)は必ず付ける
+ Content-Typeは必ず付与

# クライアントへのキャッシュ
---
+ Expiration Model(期限切れモデル)

## レスポンスヘッダ
+ Expires…古い。絶対時間指定(RFC1123)。
+ Cache-Control…現在からの秒数
+ Validation Model(検証モデル)

　条件付きリクエスト

+ If-Modified-Since
+ If-None-Match…前回レスポンスから取得保持していたETag

　レスポンス

+ 変更なしなら304でボディは空
+ Last-Modified
+ ETag…レスポンスのMD5ハッシュ等

  + ※複数リソースの際は、最後に更新されたリソースの日時

+ キャッシュさせない
+ Cache-Control: no-cache　（※厳密にはキャッシュしない指定ではない）

  + プロキシサーバへの保存もしない場合は、「no-store」を返す

+ Vary … URI以外にリクエストのユニーク特定に使うヘッダを指定

  + 例:Foursquare
    + Vary: Accept-Encoding,User-Agent,Accept-Language

  + 例:GitHub
    + Vary: Accept,Authorization,Cookie

  + (サーバ側で何をもって返すレスポンスを変えるかによる)

# 同一生成元ポリシーとCORS
---
+ クロスオリジンリソース共有

  + ①リクエストヘッダで Origin: {URL} を指定

  + ②サーバ側でOriginがアクセス許可対象なら Access-Control-Allow-Origin: {同じURL} を返す

+ CookieやAuthorizationヘッダで認証情報を送られた場合、Access-Control-Allow-Credentials: true を返す

# APIバージョンの指定
---
+ URIのパスにセマンティックバージョニングのメジャーバージョンを指定するのが一般的

# セキュリティ
---
## ■XSS対策

### レスポンス

+ Content-Type: application/json
+ (IE対策) X-Content-Type-Options: nosniff
+ XSRF対策
+ ワンタイムトークン
+ JSONハイジャック対策
+ Content-Typeを指定
+ トップレベル要素をオブジェクトにする
+ パラメータの改ざん
+ 値のチェック・・・ポイント消費にマイナス値を渡され、逆に加算となる等
+ リクエスト再送信
+ 同じアクセスの二度目からはエラーにする、短期間の複数アクセスの二度目からはカウントしない等

+ その他セキュリティ関係ヘッダ
+ X-XSS-Protection: 1; mode=block
+ X-Frame-Options: deny
  + ➡フレーム内読込禁止(クリックジャッキング防止)
+ Content-Security-Policy: default-src 'none'
  + ➡レスポンスから別生成元リソースの読込禁止
+ Strict-Transport-Security: max-age=15768000
  + ➡アクセスをHTTPSのみに限定
+ Public-Key-Pins
+ Set-Cookie: session=xxxxx; Path=/; Secure; HttpOnly

## ■レイトリミット

### 検討

+ ユーザ識別方法
+ リミット値
+ リミット値の単位
+ リミットのリセットタイミング
+ エンドポイント別等のグルーピング要否
+ プログレッシブ要否
+ 計測開始タイミング(毎時0分？ファーストアクセス？)
+ ユーザ別の制限緩和要否

### レスポンス

+ 429 Too Many Requests
+ エラー詳細をレスポンスボディに含めるべき
+ Retry-Afterヘッダで次のリクエストまでの待機時間を指定可能　※秒数or日付(RFC1123？)
  + 計測方法
    + ユーザ識別単位毎にカウントをRedisに記録

# APIドキュメント
---
+ API Blueprint・・・マークダウン方式
