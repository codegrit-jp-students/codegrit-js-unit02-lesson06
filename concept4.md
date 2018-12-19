## validateファンクションを実装する

checkFormat関数が実装出来たので、MailValidatorのvalidate関数を実施していきます。validateファンクションでは、BaseValidatorの`_cannotEmpty`ファンクションで空白でないかチェックし、その後アドレスのフォーマットを先ほど実装した`_checkFormat`ファンクションで調べ、Promiseオブジェクトを返します。BaseValidatorはMailValidatorの親クラスのため、`super.ファンクション名`という形式でファンクションを呼び出します。

```javascript
import BaseValidator from './BaseValidator';

class MailValidator extends BaseVallidator {
  constructor(val) {
    super(val, "メールアドレス"); // superクラスのコンストラクタを呼び出す。
    this._checkFormat = this._checkFormat.bind(this);
  }
  validate() {
    return super._cannotEmpty()
      .then(this._checkFormat)
      .then((res) => {
        return { success: true }; // Promise.resolve({ success: true })と同一
      })
      .catch(err => {
        return err; // Promise.resolve(err)と同一
      });
  }
  _checkFormat() {...}
}
```

## PasswordValidatorの実装

パスワードのバリデーションにはPasswordValidatorというクラスを利用します。実際のアプリケーションでは場合に応じて、最低でも大文字や記号が一文字含まれているかどうかチェックするなどが必要になってきます。今回は簡単のために文字列の長さが最低8文字以上であることをチェックする`_checkLengthファンクション`のみを定義します。validateファンクションは、MailValidatorクラスとほぼ同一です。

```javascript
import BaseValidator from './BaseValidator';

export default class extends BaseValidator {
  constructor(val) {
    super(val, 'パスワード');
    this._checkLength = this._checkLength.bind(this);
  }
  validate() {
    return super._cannotEmpty()
      .then(this._checkLength)
      .then((res) => {
        return { success: true }; // Promise.resolve({ success: true })と同一
      })
      .catch(err => {
        return err; // Promise.resolve(err)と同一
      });
  }
  _checkLength() {
    if (this.val.length >= 8) {
      return Promise.resolve();
    } else {
      return Promise.reject({
        success: false,
        message: `パスワードが短すぎます。`
      });
    }
  }
}
```

## index.jsのvalidateメソッドを実装する

ここまでで、Validatorクラスを定義しフォームデータのバリデーションを行う準備が整いました。index.jsに戻りメールアドレスとパスワードをバリデーションするためのvalidateファンクションを定義していきましょう。

validateメソッドでは、メールアドレスとパスワードの2つの妥当性を先ほど定義したValidatorクラスで確認して、結果をProm伊勢オブジェクトで返します。今回は、Promiseのレッスンで学んだ`Promise.all`メソッドを利用しましょう。

```javascript
import MailValidator from './lib/MailValidator';
import PasswordValidator from './lib/PasswordValidator';

const validate = (email, password) => {
  const mailValidator = new MailValidator(email);
  const passwordValidator = new PasswordValidator(password);
  return Promise.all([
    mailValidator.validate(),
    passwordValidator.validate()
  ]);
}

const onSubmit = () => {...}
```

## onSubmitファンクションからvalidateファンクションを呼び出す。

次に先ほどのonSubmitファンクションからvalidateファンクションを呼び出し、結果を処理していきます。onSubmit内で、validateの結果を待つ必要があるので、onSubmitはasyncファンクションへと変えます。

```javascript
...

const onSubmit = async () => {
  let emailInput = document.getElementById('email');
  let passwordInput = document.getElementById('password');
  let emailVal = emailInput.value;
  let passwordVal = passwordInput.value;
  const results = await validate(emailVal, passwordVal);
  if (results[0].success && results[1].success) {
    // バリデーション成功、ログインファンクションを呼び出す。
  } else if (results[0].success) {
    // パスワードのバリデーションに失敗
  } else if (results[1].success) {
    // メールアドレスのバリデーションに失敗
  }
    // メールアドレス、パスワード共にバリデーション失敗
  }
}
```

## サイトにエラーメッセージを表示する。

上記でバリデーション失敗した場合、ユーザーにそのことが伝わるようにしないといけません。そのために`addErrorMessage`ファンクションを実装して、サイトが更新されるようにしましょう。エラーメッセージの表示には、Bootstrapの`is-invalid`をinput要素のクラスに加え、input要素の後に`invalid-feedback`というクラスを持つdiv要素とエラーメッセージを追加します。(詳しくは[Bootstrapドキュメント内の説明](https://getbootstrap.com/docs/4.0/components/forms/#validation)をご覧ください。)

```javascript
const addErrorMessage = (type, message) => {
  let input = document.getElementById(type); // メールアドレスなら"email"、パスワードなら"password"がタイプに入る
  let val = input.val;
  input.classList.add('is-invalid'); // input要素のクラスに`is-invalid`を追加する。
  input.insertAdjacentHTML('afterend', `<div class="invalid-feedback">${message}</div>`); //input要素の後にエラーメッセージを表示する。 
}
```

onSubmitファンクションにaddErrorMessageファンクションを追加します。

```javascript
const addErrorMessage = (type, message) => {...};

const onSubmit = async () => {
  let emailInput = document.getElementById('email');
  let passwordInput = document.getElementById('password');
  let emailVal = emailInput.value;
  let passwordVal = passwordInput.value;
  const results = await validate(emailVal, passwordVal);
  if (results[0].success && results[1].success) {
    // バリデーション成功
  } else if (results[0].success) {
    addErrorMessage("password", results[1].message)
  } else if (results[1].success) {
    addErrorMessage("email", res[0].message);
  }
    addErrorMessage("email", res[0].message);
    addErrorMessage("password", res[1].message);
  }
}
```

実装出来たら、実際にフォームにバリデーションの失敗するデータを入力して試してみましょう。

```bash
$ yarn run start
...
Project is running at http://localhost:8080/
...
```

`http://localhost:8080/`を開いて、フォームに情報を入力して送信ボタンを押すとメッセージが表示されるはずです。しかし、ここで喜ぶのはまだ早いです。試しにもう一度送信ボタンを押してみましょう。すると、どんどんとエラーメッセージが追加されていってしまいます。また、妥当な情報を入力して再度送信ボタンを押してもエラーメッセージが消えません。この2つのバグを修正していきましょう。


## 送信ボタンが押されたらerrorを一旦取り除く

先ほど説明したバグを解決するために、送信ボタンが押されたらエラーメッセージを一旦取り消すファンクション`removeErrors`を実装します。

```javascript
const removeErrors = () => {
  return new Promise((resolve) => {
    // .is-invalidのついた要素を全て探し出し、.is-invalidを外す。
    document.querySelectorAll('.is-invalid').forEach((el) => {
      el.classList.remove('.is-invalid');
    })
     // .invalid-feedbackのついた要素を全て探し出し、要素自体を消去する。
    document.querySelectorAll('.invalid-feedback').forEach((el) => {
      el.parentNode.removeChild(el);
    })
    resolve();
  })
}
```

このremoveErrorsファンクションを、onSubmitメソッドで最初に呼び出します。

```javascript
const onSubmit = async () => {
  await removeErrors()
  ...
}
```

再度、サイト上で送信ボタンを教えて見て下さい。エラーメッセージの重複表示が無くなり、正しい情報を入力したらエラーメッセージが消えるようになったはずです。

- 未入力の場合

![フォーム - 未入力](./images/form_empty.png)

- バリデーション失敗時

![フォーム - バリデーション失敗](./images/form_validate_errors.png)

