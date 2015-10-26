# js workshop #3

## 概要

### 日時

2015/10/xx 19:30〜20:30

### 目的

ES6の基本文法を学ぶ。babelを用いたトランスパイル、gulpを用いた自動化を通じ、昨今のjs開発手法を活かしてみる。

### 想定参加者

* Node.jsを触ったことがある
  * ない場合は[js workshop #1](https://github.com/do7be/js_workshop_1)をやってみてください
* NPM, gulpを触ったことがある
  * ない場合は[js workshop #2](https://github.com/do7be/js_workshop_2)をやってみてください


### 内容

* git clone
* 開発環境準備(NPM, BABEL, gulp)
* ES6の基本文法を試してみる(Node)
* フロントエンドでもES6を使ってみる(browserify, babelify)


### 持ち物

ノートPC

* Mac推奨
* WinでもいいがVM構築必須
* Git環境


## workshop前の基本知識

* ES6とは
  * ECMAScript 6
  * JavaScriptの標準仕様
  * 最近は機能が細分化されたため、ECMAScript 2015と呼ばれている。
  * 各ブラウザ、Node.js、AltJS(TypeScript等)が対応を進めている。
  * 現状だとBABELというトランスパイラを使って書くのがデファクトスタンダードになっている。
    * トランスパイラ：AltJS時代に生まれた単語。特殊なJS構文を生JSに書き換えるツールのこと。BABELでは、ES6で書いたファイルをES5(これまでのJS仕様)に書き換える。


## workshop本題

### 準備

ワークショップ用リポジトリをクローンする

```bash
$ cd ${適当なディレクトリ}
$ git clone https://github.com/do7be/js_workshop_3.git
$ cd js_workshop_3
```

### 開発環境準備(NPM, BABEL, gulp)

```bash
$ npm init

適当にEnterしていく。
```

BABEL自体は`$ npm install babel`でインストールできるが、トランスパイルのたびにコマンド実行するのが面倒なので、今回はgulp-babelを使うことにする。

```bash
$ npm install --save-dev gulp
$ npm install --save-dev gulp-babel
```


```javascript
$ vi gulpfile.js

var gulp  = require("gulp"),
    babel = require("gulp-babel");

// トランスパイル処理
gulp.task('babel', function() {
  gulp.src('es6/*.js')
    .pipe(babel())
    .pipe(gulp.dest('./'))
});

// ファイル監視
gulp.task('watch', function() {
  gulp.watch('es6/*.js', ['babel'])
});

gulp.task('default', ['babel', 'watch']);
```

### ES6の基本文法を試してみる(Node)

#### let, const

ES5までは変数宣言にvarしか使うことができなかったが、ES6からはlet, constが登場した。

それぞれブロックスコープを持つ。（{}内でのみ変数が参照できる。）

* let：varのブロックスコープ版
* const：定数宣言

実際に書いてみる。

```javascript
$ gulp watch
$ vi es6/let.js

let name = 'コブラ';

if (true) {
  let name = 'ジョー・ギリアン';
  console.log(name); // 'ジョー・ギリアン'
}
console.log(name); // 'コブラ'
```

./let.jsが作成されており、ES5にトランスパイルされたことを確認し、実行してみる。

```bash
$ node let.js
```

#### Arrow Function

ES5までのJSでは式の中で関数宣言する場合に以下のような宣言をしていた。

```javascript
var hoge = function() {
  ;
}

var piyo = function(a, b) {
  ;
}

var foo = function(a) {
  return a * a;
}
```

ES6では以下のようにfunctionを記述することができる。

```javascript
let hoge = () => {
  ;
}

let piyo = (a, b) => {
  ;
}

let foo = a => a * a;
```

実際に書いてみる。

```javascript
$ vi es6/arrow.js

let add = (a, b) => {
  return a + b;
};

let result = add(1, 2);
console.log(result);
```

トランスパイルされた./arrow.jsを確認し、実行してみる。

```bash
$ node arrow.js
```

#### Temlate strings

ES5では下記のように文字列内に変数を入れるのがすごく面倒だった。

```javascript
var printProfile = function (firstName, lastName, old) {
  console.log('姓：' + lastName + ' 名：' + firstName + ' 年齢：' + old);
}

printProfile('蛍', '一条', 11);
```

ES6では下記のように書ける。

```javascript
`${hoge}です！`
```

実際に書いてみる。

```javascript
$ vi es6/template.js
let printProfile = (firstName, lastName, old) => {
  console.log(`姓：${lastName} 名：${firstName} 年齢：${old}`);
}

printProfile('蛍', '一条', 11);
```

トランスパイルされた./template.jsを確認し、実行してみる。


#### Class

ES5まではJavaScriptにはclassが存在しなかった。代わりにprototypeというものを使って無理やりclassのような使い方をしていた。

ES6からはclass構文がサポートされた。

```javascript
$ vi es6/class.js

class Animal {
  constructor(sound) {
    this.sound = sound;
  }

  // メソッド
  say() {
    console.log(this.sound);
  }

  // staticメソッド
  static kind() {
    console.log('哺乳類');
  }
}

let dog = new Animal('ワン');
dog.say();

Animal.kind()
```

トランスパイルされた./class.jsを確認し、実行してみる。


#### Module export, import

Node.jsでお馴染みのModuleをimportすることができるようになった。


```javascript
$ es6/import.js

import * as module from './module';
module.Human.serif(module.defaultName);
```

```javascript
$ es6/module/export.js

export const defaultName = '島村卯月';

export class Human {
  static serif(name) {
    console.log(`${name}、がんばります！`);
  }
}
```

トランスパイルされた./import.js, ./export.jsを確認し、./import.jsを実行してみる。


ES6の文法はまだまだあるが、時間がないので次回やることにする。



### フロントエンドでもES6を使ってみる(browserify, babelify)

gulpfile.jsにて同じようにトランスパイルしても、importなどはrequireに変換されるため、フロントエンド(ブラウザ側)からは利用することが出来ない。

なのでトランスパイルしたJSファイルをさらにブラウザで使えるように変換する必要がある。

browserifyというパッケージを用いる。

```bash
$ npm install --save-dev babelify browserify
```

gulpfile.jsの要所要所に下記を追記する。

```javascript
// パッケージ宣言
var browserify = require('browserify'),
    babelify   = require('babelify');

// フロントエンド用トランスパイルのタスク宣言
gulp.task('browserify', function() {
  browserify('front/es6/*.js', { debug: true })
    .transform(babelify)
    .bundle()
    .on("error", function (err) { console.log("Error : " + err.message); })
    .pipe(source('bundle.js'))
    .pipe(gulp.dest('front/'))
});

// ファイル監視に追加
gulp.watch('front/es6/*.js', ['browserify'])
```