## フォームデータを扱う

フォームについてはHTMLのレッスンで既に学んできました。このレッスンでは、Javascriptを利用してフォームに入力された情報を受け取り、入力されたデータが妥当かどうかを確認し(バリデーション)、最後にデータストアに保存するまでの流れを説明していきます。今回は簡単に、ログインフォームを作ってみます。


## スターターファイル

- [codegrit-jp-students/js-unit02-lesson06-sample01-starter](https://github.com/codegrit-jp-students/js-unit02-lesson06-sample01-starter)


## フォーム提出の流れ

以下は、フォームデータの取得から提出の流れです。

1. 送信ボタンのクリックイベントが起こる。
2. フォームに入力された値を得る。
3. 得た値が妥当かどうかチェックする。
4. チェックした値が妥当で無ければエラーを表示する。
5. 値が妥当であれば、フォーム情報をサーバーに送信する。
6. サーバーから帰ってきたデータを表示する。

## server.js

今回のスターターファイルにはserver.jsというファイルが含まれています。このserver.jsはNode.jsとNode.js製のフレームワークExpress.jsを使って実装しています。

`npm run start`のコマンドで、このserver.jsと、webpack-dev-serverが同時に立ち上がり、`http://localhost:3000/login/`というルートで仮想的にログイン機能を試せるようにしています。

今回は、以下のメールアドレスとパスワードのみ受け付けるようにしています。

- メールアドレス: testuser@codegrit.jp
- パスワード: password

メールアドレスとパスワードをfetchで送信し、ログイン成功時には、ステータスコード200で、以下のオブジェクトをJSON化したものが返ります。

```javascript
{
  "id": 1,
  "name": "Code Grit",
  "email": "testuser@codegrit.jp",
  "message": "ログイン成功！"
}
```

ログイン失敗時には、ステータスコード403で以下のオブジェクトをJSON化したものが返ります。

```javascript
{
  message: "ログイン失敗"
}
```

では、ここからはフロントエンド側のコードを実際に書いていきましょう。