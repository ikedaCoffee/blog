---
title: "jQuery入門"
date: 2019-12-17T15:06:05+09:00
draft: false
toc: false
images:
  - https://picsum.photos/1024/768/?random
tags:
  - jQuery
---

[Progate](https://prog-8.com/languages/jquery)でjQueryを勉強したときの自分用のメモです。かなり基礎的な内容です。

## 高速化のためにできること

- 同じjQueryオブジェクトを複数回使用する時は変数にする。

  ```javascript
  var $div = $('div');
  ```

- 同じjQueryオブジェクトを複数回使用する時は、メソッドチェーンを使うことでも処理速度を高速化できる。 $('セレクタ').メソッド().メソッド()...のように書くこと 。

  ```javascript
  $('div').css('color', 'red').html('jQuery');
  ```

## 知識

- jQueryはHTMLの中身を操作するため、HTMLの読み込みが完了してからjQueryによる操作を開始するようにする。 そのためにはreadyイベントを使用し、`$(document).ready()`の中身にjQueryの処理を書いていく。 この構文には省略形も用意されており、`$(function(){ });`と書くことも出来る。

  ```javascript
  $(document).ready(function) {
    // この中にjQueryの処理を書く
  });
  ```

  ```javascript
  $(function() {
    // この中にjQueryの処理を書く
  });
  ```

- HTMLのタグには`class`や`id`、`src`といったものを指定できるが、これらを属性と呼ぶ。

## メソッド / イベント

- `find`メソッドは、すべての子孫要素(自分よりも下の階層の要素すべて）の中から、指定したセレクタを持つ要素を取得したいときに用いる。

- `children`メソッドは、指定したセレクタが持つ子要素（一階層だけ下）の中から指定したセレクタに合致した要素を取得したいときに用いる。

- `css`メソッドは、CSSを変更できるメソッド。1つ目の引数にCSSのプロパティを、2つ目の引数にプロパティの値をいれる。二つ目の引数がない場合は、CSSのプロパティの値を取得する。

- `html`メソッドは、要素の中身のHTMLを書き換えることが出来る。textメソッドと違い、htmlメソッドの引数は、単なる文字列ではなくHTMLだということである。引数がない場合は、要素の中身のHTMLを取得する。

- `text`メソッドは、`$('セレクタ').text('書き換える文字列');`のように記述して、要素内の文字列を「セット」することができる。`text`メソッドは引数を指定せずに用いることで、要素内の文字列を「ゲット」することもできる。

  ```html
  <h1>おはよう</h1>
  <p>さようなら</p>

  <script>
    $('h1').text('こんばんは') // h1要素内(おはよう)を引数の文字列(こんばんは)で変更
    $('p').text(); // p要素内の文字列(さようなら)を取得
  </script>
  ```

- HTMLの属性は`attr`メソッドを用いて「ゲット」と「セット」ができる。例えば、`attr('id', 'title')`のように第一引数に属性名、第二引数にその属性値を指定することで属性をセットすることができる。そして、第二引数を指定しない場合は、その属性の値を取得できる。

  ```html
  <h1>こんにちは</h1>
  <a href="https://www.google.co.jp"></a>

  <script>
    // h1要素にtitleというidをセット
    $('h1').attr('id', 'title');
    // a要素のhref属性の値(https://www.google.co.jp)をゲット
    var url = $('a').attr('href')
  </script>
  ```

- `hover`イベント

  ```javascript
  $('$id').hover(
    function() {
      // マウスをのせた時の処理
    },
    function() {
      // マウスが外れた時の処理
    }
  );
  ```

- `addClass`メソッドを用いると、指定した要素にクラスを追加することができる。

  ```javascript
  $('.text-contents').addClass('text-active');
  ```

- `removeClass`メソッドを用いると、指定した要素から指定したクラスを取り除くことができる。

  ```javascript
  $('.text-contents').removeClass('text-active');
  ```

- `hasClass`メソッドは、引数に指定したクラスを、オブジェクトが持っているか判定するときに使用する。 オブジェクトがそのクラスを持っていれば`true`、持っていなければ`false`を返す。

  ```html
  <div class="answer1"></div>
  <div class="answer2 open"></div>
  <script>
    $('.answer1').hasClass('open'); // false
    $('.answer2').hasClass('open'); // true
  </script>
  ```

- `show`メソッドは要素を表示させる。

  `hide`メソッドは要素を非表示させる。

- `slideDown`メソッドは要素を表示させる。

  `slideUp`メソッドは要素を非表示させる。

- `fadeIn`メソッドは要素を表示させる。

  `fadeOut`メソッドは要素を非表示させる。

- `eq`メソッドを用いると、jQueryオブジェクトの中から、`eq`の引数の数字と同じインデックス番号の要素を取得できる。

  ```html
  <ul>
    <li>インデックス0</li>
    <li>インデックス1</li>
    <li>インデックス2</li>
  </ul>

  <script>
    $('li').eq(2).text(); // インデックス2
  </script>
  ```

- `index`メソッドを用いると、↓のコードのように「li」要素の中の「.active」要素のインデックス番号（1）を取得できる。

  ```html
  <ul>
    <li>リスト0</li>
    <li>リスト1</li>
    <li>リスト2</li>
  </ul>

  <script>
    $('li').index($('.active')); // 1
  </script>
  ```

  `click`イベントの中では、`$(this)`にはクリックした要素が入っている。それを`index`メソッドの引数に指定することで、クリックした要素のインデックス番号を取得することができる。

  ```html
  <ul>
    <li>リスト0</li>
    <li>リスト1</li>
    <li>リスト2</li>
  </ul>

  <script>
    $('li').click(function() {
      var index = $('li').index($(this)); // クリックしたli要素のインデックス番号が代入される
    });
  </script>
  ```

- `prev`メソッドは、jQueryオブジェクトの兄弟要素（同じ階層の要素）の中から一つ前の要素を、

  `next`メソッドは一つ後ろの要素を取得することができる。

- `val`メソッド。 `input`タグを用いると一行の入力欄を作ることができる。`input`タグに入力されている値は、`val`メソッドで取得できる。

  ```html
  <form>
    <div>名前:</div>
    <input id="name" type="text">
  </form>

  <script>
    var name = $('#name').val(); // フォームに入力した値が取得できる
  </script>
  ```

- `submit`イベント。フォームが送信されたときのイベントとして、`submit`イベントがある。`submit`イベントを用いると、送信ボタンがクリックされたときだけでなく、「Enter」キーでフォームが送信された場合もイベントが発生する。

- `animate`メソッドを用いてアニメーションをつけることができる。`$('セレクタ').animate({'プロパティ':'値'})`のように、引数は連想配列で指定する。
  2つ目の引数でアニメーションの時間を設定することができる。時間はミリ秒で指定するか、'slow'や'fast'といった文字列で指定することができる。

  ```javascript
  $('h1').animate({
    'font-size': '50px'
  }, 1000);
  ```

  `animate`メソッドでは、CSSの値だけでなく、`scrollTop`などの値も変更できる。
  `$('html, body').animate({'scrollTop': 0}, 時間);` のように指定する。

- `scrollTop`メソッドは、`$('html, body').scrollTop(値);` のように指定し、ページ最上部から`値px`の位置に移動することができる。`scrollTop`は通常`$('html, body')`に対して用いるので、セットで覚えておくとよい。

- `offset`メソッドを使用することで、要素の表示位置を取得することができる。`offset`メソッドは、ページの上端からの距離とページの左端からの距離が連想配列の形で返ってくる。`offset().top`とすると、ページの上端からの距離が取得できる。

  ```javascript
  // h1要素の表示位置を取得
  $('h1').offset(); // { top: 180, left: 100 }
  // ページ最上部からの、h1要素の距離を取得
  $('h1').offset().top; // 180
  ```

  各ボタンはaタグにし、そのhref属性に飛び先のid名を指定しておく。そしてクリックしたときにattrメソッドでhref属性の値を取得し、offsetメソッドでその要素の位置を取得することでページ内リンクを実装できる。

  ```html
  <!-- ページ内リンクの実装 -->
  <a class="scroll-btn" href="#hogehoge">HTML</a>

  <div id="hogehoge">ほげほげ</div>

  <script>
    $('.scroll-btn').click(function() {
      var id = $(this).attr('href');
      var position = $(id).offset().top;
    });
  </script>
  ```
