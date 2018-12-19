## サーバーにデータを送信する

次に、フォームに入力されたデータをサーバーに送信しなければなりません。これには、レッスン3で学んだfetch APIを利用します。まずは、fetch APIのpolyfillである`whatwg-fetch`パッケージをyarnを利用して追加します。

```bash
$ yarn add whatwg-fetch
```

追加したら、index.js内で、loginのためのファンクションを実装していきましょう。サーバー側のコードはプロジェクトフォルダのserver.jsから確認出来ますので興味のある方はご覧下さい。

```javascript

import fetch from 'whatwg-fetch';

const endpoint = 'http://localhost:3000';

const login = (email, password) => {
  return new Promise((resolve, reject) => {
    fetch(`${endpoint}/login`, {
      method: 'post',
      headers: {
        Accept: 'application/json',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        email: email,
        password: password
      })
    })
    .then((res) => {
      const json = res.json();
      if (res.status === 200) { // ログイン成功
        return json
      } else { // ログイン失敗
        return Promise.reject(new Error('ログイン失敗'))
      }
    })
  })
}
```

上記のコードでは、fetchAPIを利用してログインルートにPOSTメソッドでメールアドレスとパスワードを送信しています。

次にサーバー側から戻ってきたresponseのレスポンスボディ部分のJSONデータにres.json()というファンクションでアクセスし、これを返しています。

## ログインの結果を処理する

最後にloginファンクションの結果をonSubmitファンクション上で処理していきます。

```javascript

const onSubmit = async () => {
  await removeErrors()
  let emailInput = document.getElementById('email');
  let passwordInput = document.getElementById('password');
  let emailVal = emailInput.value;
  let passwordVal = passwordInput.value;
  const results = await validate(emailVal, passwordVal);
  if (results[0].success && results[1].success) {
    login(emailVal, passwordVal)
    .then((json) => {
      alert(json.message);
    })
    .catch((err) => {
      alert(err.message);
    });
  } else if (results[0].success) {
    addErrorMessage("password", results[1].message);
  } else if (results[1].success) {
    addErrorMessage("email", results[0].message);
  } else {
    addErrorMessage("password", results[1].message);
    addErrorMessage("email", results[0].message);
  }
}
```

これで必要な機能が全て実装完了しました。

## 実際に動かす

さて、ここまで完成したら実際に動かしましょう。バリデーションエラーメッセージの表示、正しいパスワードでログイン成功、誤ったパスワードでのログイン失敗のアラート表示がそれぞれあれば成功です。

![フォーム - ログイン成功](./images/form_login_success.png)

## 完成版のコード

- [codegrit-jp-students/js-unit02-lesson06-sample01-final](https://github.com/codegrit-jp-students/js-unit02-lesson06-sample01-final)