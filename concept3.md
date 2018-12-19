## 正規表現とは

正規表現とは、文字列内で文字の組み合わせを照合するために用いられるパターンです。英語ではRegular Expressionといいます。Javascriptでは正規表現(RegExp)オブジェクトを利用して正規表現を処理します。

RegExpオブジェクトは、正規表現リテラルを用いるか、あるいはコンストラクタ関数を利用して作成することが出来ます。

```javascript
// 正規表現リテラル利用。この場合パターンの左右をスラッシュ/で囲みます。
const re = /[a-z]+/;

// コンストラクタ関数を利用
const re = new RegExp('[a-z]+');
```

上記で、`[a-z]+`の部分は何を意味しているかというと、`[a-z]`の部分は小文字のアルファベットを示し、`+`の部分は一文字以上それが続くことを示しています。

文字列中で、パターンが合致する部分があるかどうかをチェックするには`test`とうインスタンスメソッドを利用します。

```javascript
let str1 = "ABC";
let str2 = "Abc";

console.log(re.test(str1)); // false
console.log(re.test(str2)); // true
```

上記のように、str2は"bc"というアルファベットの小文字が続いているため、`re`で指定したパターンとマッチします。

### 正規表現のパターン

正規表現で利用するパターンには以下のようなものがあります。

- **^** - 文字列の先頭を表します。例えば`^[a-z]+`の場合は先頭のすぐ後にアルファベットの小文字が1文字以上来ることを表します。

```javascript
const re = /^[a-z]+/

let str1 = "abc";
let str2 = "Abc";

console.log(re.test(str1)); // true
console.log(re.test(str2)); // false
```

- **$** - 文字列の末尾を表します。例えば`[a-z]$`は、文字列の末尾が小文字のアルファベットであることを表します。

```javascript
const re = /[a-z]+$/;

let str1 = "abC";
let str2 = "Abc";

console.log(re.test(str1)); // false
console.log(re.test(str2)); // true
```

- **x|y** - xまたはyに合致することを表します。

```javascript
const re = /(xyz|abc)$/; // 末尾がabcまたはxyzなら合致

let str1 = "abcabc";
let str2 = "abcdef";
let str3 = "abcxyz";

console.log(re.test(str1)); // true
console.log(re.test(str2)); // false
console.log(re.test(str3)); // true
```

- [] - 【】内のいずれかの文字を表します。例えば[a-zA-Z]は小文字か大文字のアルファベットを表しますが、数字や表しません。数字を表したい場合は[0-9]とします。


```javascript
const re = /[A-Z0-9]$/; // 末尾が大文字のアルファベットか数字なら合致

let str1 = "abc";
let str2 = "abC";
let str3 = "abc1";

console.log(re.test(str1)); // false
console.log(re.test(str2)); // true
console.log(re.test(str3)); // true
```

### フラグ

また、正規表現を指定する際にフラグを用いることでより高度な検索を行うことが出来ます。フラグは以下のようにして指定します。例えば以下では大文字と小文字を区別しないiというフラグを指定しています。

```javascript
const re = /[a-z]+/i

let str1 = "abc";
let str2 = "ABC";

console.log(re.test(str1)); // true
console.log(re.test(str2)); // true
```


正規表現については、それだけで一冊の本が書ける内容であり、また必要がある時に勉強するという手法を取るのが良いかと思いますので、このレッスンではこの程度にとどめます。今後、プログラムを書いていく中で文字列のパターン合致が必要になった時に更に詳しく勉強することを推奨します。


## メールアドレスのフォーマットをチェックする

メールアドレスはよく使うものですので、既によく吟味されたパターンが公開されています。今回はそうしたパターンの一つを使います。

```javascript
const re = /^([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})$/i;
```

このパターンを利用して、_checkFormatファンクションを実装していきます。


```javascript

import BaseValidator from './BaseValidator';

class MailValidator extends BaseVallidator {
  constructor(val) {
    super(val, "メールアドレス"); // superクラスのコンストラクタを呼び出す。
  }
  _checkFormat() {
    const re = /^([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})$/i; // 正規表現
    const match = re.test(this.val); // マッチするならtrue、しないならfalseを返す。
    if (match) {
      return Promise.resolve();
    } else {
      return Promise.reject({
        success: false,
        message: `${this.type}のフォーマットが異なります。`
      })
    }
  }
}
```