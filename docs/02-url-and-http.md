---
marp: true
---

# Webページが表示されるまで

第2回 URLとHTTP

---

## あなたは今からGitHubを見ます

1. ブラウザを開く
2. アドレスバーに `github.com` と入力
3. `Return` / `Enter` を押す

ページを見るときには、ブラウザがURLを基にHTTPリクエストを送信している。
アドレスバーからではなく、リンクをクリックしても（概ね）同じ。

---

## URLとは

- Uniform Resource Locator ([RFC 1738](https://datatracker.ietf.org/doc/html/rfc1738))

リソースの場所を示すための文字列。いくつかの要素から構成される。

特にWeb(HTTP)では、次のような構成になる。

```
http://example.com:80/path/to/resource?query#fragment
^      ^           ^  ^               ^     ^
|      |           |  |               |     └ フラグメント
|      |           |  |               └ クエリ
|      |           |  └ パス
|      |           └ ポート
|      └ ドメイン名
└ プロトコル
```

---

## ドメイン名の解決

URLを見ただけでは、どのサーバーにアクセスすればいいかわからないので、ドメインからサーバーを特定する。

DNS (Domain Name System) は、ドメイン名をIPアドレスに変換するための仕組み。

```
         example.com のIPアドレス教えて
┌───────┐                            ┌───────┐
│User   ├───────────────────────────►│DNS    │
│       │◄───────────────────────────│       │
└───────┘                            └───────┘
                   XXX.XXX.XXX.XXXです
```

`nslookup example.com` などで確認できる。

---

## HTTPリクエスト

DNSでサーバーのIPアドレスが取得できたので、HTTPリクエストを送信する。

HTTPリクエストは、次のような構成になる。

```
GET /path/to/resource?query HTTP/1.1
Host: example.com
```

---

## 実際に見てみる

curlコマンドを使うことで、HTTPリクエストを手軽に送信できる。

curlコマンドの使い方がわからないので、manコマンドを使ってみる。

```sh
man curl
```

が、めちゃくちゃ長いので、生成AIに聞くのがおすすめ。

```sh
curl -v http://example.com/
```

---

## 実際に見てみる2

```
> GET / HTTP/1.1
> Host: example.com
> User-Agent: curl/8.4.0
> Accept: */*
>
```

一般化すると

```
method request-target HTTP-version
field-name: field-value
field-name: field-value
```

([BNF](https://datatracker.ietf.org/doc/html/draft-ietf-httpbis-messaging#name-collected-abnf))

---

## HTTPレスポンス

サーバーはHTTPリクエストに対して、HTTPレスポンスを返す。

HTTPレスポンスは、次のような構成になる。

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234

<!DOCTYPE html>...
```

---

## 実際に見てみる3

```
< HTTP/1.1 200 OK
< Accept-Ranges: bytes
< Age: 269183
< ...
<
<!DOCTYPE html>...
```

一般化すると

```
HTTP-Version status-code [reason-phrase]
field-name: field-value
field-name: field-value

message-body
```

([BNF](https://datatracker.ietf.org/doc/html/draft-ietf-httpbis-messaging#name-collected-abnf))

---

## HTTP1はテキスト

HTTP1.0/1.1はテキストベースのプロトコル。怖くない。
すべてのHTTPリクエスト/レスポンスはこれに準ずる形式でやり取りされていることがイメージできると、フロントエンドフレームワークの世界を出なければいけないときの理解スピードが上がる。

JavaScriptのfetchやXHRも当然この形式。GraphQLやconnect(gRPC)もこの上に成り立っている。

（HTTP2はストリームの再利用などバイナリベースで構築されている）

---

## セッションやContent-Type、CORS

いわゆる“セッション”は、実態はCookieで、
Cookieの実態はHTTPヘッダー。

```
Set-Cookie: session=abcdef; Path=/; Expires=Wed, 09 Jun 2021 10:18:14 GMT
```

Content-TypeもHTTPヘッダー、CORSで用いるOrigin、Access-Control-Allow-OriginなどもHTTPヘッダー。

```
Content-Type: text/html; charset=utf-8
Access-Control-Allow-Origin: *
```

---

## まとめ

Webページを閲覧するとき、ブラウザがURLを基にHTTPリクエストを送信、HTTPレスポンスを受信している。

その実態は、(元は)テキストベースのHTTPプロトコル。
