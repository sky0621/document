# エンドポイント設計
---
+ HTTPｽｷｰﾑからﾘｿｰｽ名までのURI
+ 名詞の複数形を使う
+ 「api.～～」


# メソッド
---
+ PUTはﾘｿｰｽの上書き

+ PATCHはﾘｿｰｽの一部変更


# パス
---
+ 単語のつなぎは極力作らずﾊﾟｽで分ける。できない場合は単語間をﾊｲﾌﾝでつなぐｹｰｽが多い
+ 検索条件(及び、指定を省略しても成り立つもの)はｸｴﾘﾊﾟﾗﾒｰﾀで指定

##### 　　　★ﾌｨｰﾙﾄﾞ1つなら「q=」

+ ﾍﾟｰｼﾞﾈｰｼｮﾝはlimitとoffsetを使う

##### ★ﾊﾟﾌｫｰﾏﾝｽ問題

　★ﾃﾞｰﾀのズレ
　➡max_id使う

# 認証
---
+ APIの認可はOAuth2.0を使う
+ Authorization Code = ｻｰﾊﾞｻｲﾄﾞWebｱﾌﾟﾘ
+ Implicit = ｽﾏﾎｱﾌﾟﾘ/JSｱﾌﾟﾘ
+ Resourse Owner Password Credentials = 別ｻｰﾊﾞｻｲﾄﾞｱﾌﾟﾘ使わず直接ｻｰﾊﾞｻｲﾄﾞｱﾌﾟﾘにﾊﾟｽ認証でﾄｰｸﾝｹﾞｯﾄ

　　★以下をPOST

　　+ grant_type="password"

　　+ username

　　+ password

　　+ scope ... ｱｸｾｽｽｺｰﾌﾟ(省略可)

　　　※Authorizationﾍｯﾀﾞでｻｰﾋﾞｽ毎の認証情報(ID/ﾊﾟｽをBase64変換)

　　➡ｻｰﾊﾞからのﾚｽﾎﾟﾝｽ

　　+ access_token

　　+ token_type: bearer
　　+ expires_in

　　+ refresh_token

+ Client Credentials = ﾕｰｻﾞ単位で認可しないｱﾌﾟﾘ

# レスポンス
---
+ 基本はJSON
+ ﾘｸｴｽﾄﾍｯﾀﾞAcceptでapplication/json と絞り込める
+ ﾃﾞｰﾀ構造
+ 通信減らす
+ なるべくﾌﾗｯﾄ
+ ﾄｯﾌﾟﾚﾍﾞﾙは配列でなくｵﾌﾞｼﾞｪｸﾄ(JSONｲﾝｼﾞｪｸｼｮﾝ対策)
+ ﾍﾟｰｼﾞﾈｰｼｮﾝ…20件表示なら21件取ってみると、21件目の有無で次ﾍﾟｰｼﾞ要否判定可能
+ JSONはｷｬﾒﾙｹｰｽで命名
+ 性別(生物学的)…M(男性)、F(女性)、U(不明orその他)
　※社会的な性別(gender)の時は多様
+ 日付ﾌｫｰﾏｯﾄはRFC3339(W3C-DTF)が標準
　＝2017-07-19T08:30:49+09:00
　※ﾀｲﾑｿﾞｰﾝはUTC(+00:00またはZ)
+ DB構造のままでなくﾕｰｽｹｰｽに応じた設計
+ ｴﾗｰ詳細…ｽﾃｰﾀｽｺｰﾄﾞ500等の際にﾚｽﾎﾟﾝｽﾎﾞﾃﾞｨに詳細ｺｰﾄﾞを含めるか、詳細ﾄﾞｷｭﾒﾝﾄへのﾘﾝｸを含めるか、ﾕｰｻﾞ向けと開発者向けのﾒｯｾｰｼﾞを分けるか検討
+ ｴﾗｰの際にHTMLが返らないようにする
+ ﾒﾝﾃﾅﾝｽ時は503とHTTPﾍｯﾀﾞ(Retry-After: Mon, 2 Dec 2013 03:00 GMT等)
+ Dateﾍｯﾀﾞ(HTTP時間＝RFC1123でﾀｲﾑｿﾞｰﾝはGMTのみ)は必ず付ける
+ Content-Typeは必ず付与

# クライアントへのキャッシュ
---
+ Expiration Model(期限切れﾓﾃﾞﾙ)

## レスポンスヘッダ

+ Expires…古い。絶対時間指定(RFC1123)。
+ Cache-Control…現在からの秒数
+ Validation Model(検証ﾓﾃﾞﾙ)

　条件付きﾘｸｴｽﾄ

+ If-Modified-Since
+ If-None-Match…前回ﾚｽﾎﾟﾝｽから取得保持していたETag

　ﾚｽﾎﾟﾝｽ

+ 変更なしなら304でﾎﾞﾃﾞｨは空
+ Last-Modified
+ ETag…ﾚｽﾎﾟﾝｽのMD5ﾊｯｼｭ等

　※複数ﾘｿｰｽの際は、最後に更新されたﾘｿｰｽの日時

+ ｷｬｯｼｭさせない
+ Cache-Control: no-cache

　　※厳密にはｷｬｯｼｭしない指定ではない

　　　ﾌﾟﾛｷｼｻｰﾊﾞへの保存もしない場合は、

　　　no-store を返す

+ Vary … URI以外にﾘｸｴｽﾄのﾕﾆｰｸ特定に使うﾍｯﾀﾞを指定

　　例:Foursquare

　　　Vary: Accept-Encoding,User-Agent,Accept-Language

　　例:GitHub

　　　Vary: Accept,Authorization,Cookie

　　(ｻｰﾊﾞ側で何をもって返すﾚｽﾎﾟﾝｽを変えるかによる)

# 同一生成元ポリシーとCORS
---
+ ｸﾛｽｵﾘｼﾞﾝﾘｿｰｽ共有

　①ﾘｸｴｽﾄﾍｯﾀﾞで Origin: {URL} を指定

　②ｻｰﾊﾞ側でOriginがｱｸｾｽ許可対象なら Access-Control-Allow-Origin: {同じURL} を返す

+ CookieやAuthorizationﾍｯﾀﾞで認証情報を送られた場合、Access-Control-Allow-Credentials: true を返す

# APIバージョンの指定
---
+ URIのﾊﾟｽにｾﾏﾝﾃｨｯｸﾊﾞｰｼﾞｮﾆﾝｸﾞのﾒｼﾞｬｰﾊﾞｰｼﾞｮﾝを指定するのが一般的

# セキュリティ
---
+ XSS対策

　ﾚｽﾎﾟﾝｽ

+ Content-Type: application/json
+ (IE対策) X-Content-Type-Options: nosniff
+ XSRF対策
+ ﾜﾝﾀｲﾑﾄｰｸﾝ
+ JSONﾊｲｼﾞｬｯｸ対策
+ Content-Typeを指定
+ ﾄｯﾌﾟﾚﾍﾞﾙ要素をｵﾌﾞｼﾞｪｸﾄにする
+ ﾊﾟﾗﾒｰﾀの改ざん
+ 値のﾁｪｯｸ…ﾎﾟｲﾝﾄ消費にﾏｲﾅｽ値を渡され、逆に加算となる等
+ ﾘｸｴｽﾄ再送信
+ 同じｱｸｾｽの二度目からはｴﾗｰにする、短期間の複数ｱｸｾｽの二度目からはｶｳﾝﾄしない等

+ その他ｾｷｭﾘﾃｨ関係ﾍｯﾀﾞ
+ X-XSS-Protection: 1; mode=block
+ X-Frame-Options: deny
　　➡ﾌﾚｰﾑ内読込禁止(ｸﾘｯｸｼﾞｬｯｷﾝｸﾞ防止)
+ Content-Security-Policy: default-src 'none'
　　➡ﾚｽﾎﾟﾝｽから別生成元ﾘｿｰｽの読込禁止
+ Strict-Transport-Security: max-age=15768000
　　➡ｱｸｾｽをHTTPSのみに限定
+ Public-Key-Pins
+ Set-Cookie: session=xxxxx; Path=/; Secure; HttpOnly

+ ﾚｲﾄﾘﾐｯﾄ

　検討

　+ ﾕｰｻﾞ識別方法
　+ ﾘﾐｯﾄ値
　+ ﾘﾐｯﾄ値の単位
　+ ﾘﾐｯﾄのﾘｾｯﾄﾀｲﾐﾝｸﾞ
　+ ｴﾝﾄﾞﾎﾟｲﾝﾄ別等のｸﾞﾙｰﾋﾟﾝｸﾞ要否
　+ ﾌﾟﾛｸﾞﾚｯｼﾌﾞ要否
　+ 計測開始ﾀｲﾐﾝｸﾞ(毎時0分？ﾌｧｰｽﾄｱｸｾｽ？)
　+ ﾕｰｻﾞ別の制限緩和要否

　ﾚｽﾎﾟﾝｽ

　+ 429 Too Many Requests
　+ ｴﾗｰ詳細をﾚｽﾎﾟﾝｽﾎﾞﾃﾞｨに含めるべき
　+ Retry-Afterﾍｯﾀﾞで次のﾘｸｴｽﾄまでの待機時間を指定可能
　　　※秒数or日付(RFC1123？)
　計測方法
　+ ﾕｰｻﾞ識別単位毎にｶｳﾝﾄをRedisに記録

# APIドキュメント
---
+ API Blueprint…ﾏｰｸﾀﾞｳﾝ方式
