# jQuery Migration
## jQuery1.x／2.xで作成したアプリをjQuery 3に対応させる
2006年8月に1.0がリリースされてから10年となる2016年6月、メジャーバージョンアップとなるjQuery 3.0がリリースされました。従来、jQueryでは以下のバージョンが並行して開発されていました。  

- 1.x： 互換性重視（Internet Explorer 8以前もサポート）
- 2.x： 軽量化優先（モダンブラウザーのみをサポート）

しかし、マイクロソフトがInternet Explorer8以前のサポートを終了したことで、新しいjQuery3では以下のモダンブラウザーのみをサポートすることになります。

- Chrome／Firefox／Edge／Safari： 最新安定版と、その1つ前のバージョン
- Internet Explorer： 9以上
- Opera： 最新安定版のみ
- Android（標準ブラウザー）： 4.0以上
- iOS（Safari）： 7以上

jQuery 3のリリースに伴い、今後はjQuery 1.x／2.x系についても新機能の追加などは行われませんので注意してください。セキュリティフィックスは当面継続されるようですが、今後の開発では、対応すべきブラウザーとも相談しながら、徐々にjQuery 3に移行していくべきでしょう。

### NOTE jQuery3の構成
jQuery 3では、従来のProduction版（圧縮版）、Development版（非圧縮版）に加えて、Slim版が提供されます。
- Production版： `https://code.jquery.com/jquery-3.0.0.min.js`
- Development版： `https://code.jquery.com/jquery-3.0.0.js`
- Slim版： `https://code.jquery.com/jquery-3.0.0.slim.min.js`

Slim版は、本来のjQueryからAjax、Effect、また、非推奨となった機能を除外して、サイズを低減したバージョンです。Production版の85KBytesに対して、Slim版は68KBytesとなります。

## jQuery 3への移行
jQuery 3はメジャーバージョンアップということで、（限定的であるとはいえ）破壊的な変更がいくつかなされています。以下に、主な変更点をまとめます。

- Strictモードのサポート
- 非推奨の`load`／`unload`／`error`を削除（＝`on`メソッドで代替）
- 非推奨の`context`／`selector`メソッドを削除
- `ready`イベントリスナーの非同期化
- `$.isNumeric`メソッドの動作変更
- ECMAScript 2015の`for...of`ループへの対応
- カスタムセレクターの高速化

実際、jQueryの公式ブログ（英語）でも「Despite the 3.0 version number, we anticipate that these releases shouldn't be too much trouble when it comes to upgrading existing code.」（3.0というバージョン番号にもかかわらず、われわれは既存のコードをアップグレードした際にもさほどのトラブルはないだろうと考えている）と述べられており、これまでのアプリが致命的に影響を被る状況は少ないでしょう。しかし、それでもメジャーバージョンアップともなれば思わぬトラブルの発生が気になるところ。そこで、jQueryでは、スムーズにjQuery 3に移行できるよう、**jQuery Migration**と呼ばれる移行ライブラリを提供しています。

jQuery Migrationを利用するには、以下のようにページでjQuery Migrateをインポートするだけです。jQuery MigrateはjQueryのプラグインなので、jQuery⇒jQuery Migrateの順でインポートしなければなりません。

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8" />
<title>jQuery TIPS</title>
</head>
<body>
<div id="list">
  <a href="https:///re.buildinsider.net/web/jqueryref/index/icon.s.png">
    jQuery</a>
  <a href="https:///re.buildinsider.net/web/angularjstips/index/icon.s.png">
    AngularJS</a>
  <a href="https:///re.buildinsider.net/web/reacttips/index/icon.s.png">
    React</a>
</div>
<hr />
<img id="result"  />
<script src="https://code.jquery.com/jquery-3.0.0.min.js"></script>
<!--jQuery Migrateを有効化-->
<script src="https://code.jquery.com/jquery-migrate-3.0.0.min.js"></script>
<script>
$(function() {
  // 画像を取得できない場合はデフォルトの画像を表示
  $('#result').error(function() {
    $(this).attr('src', 'no_image.gif');
  })
  // 初期状態では最初のリンク先画像を表示
  .attr('src', $('#list a:first').attr('href'));

  // リンククリック時にhref属性の内容を<img>要素に反映
  $('#list a').click(function(e) {
    $('#result').attr('src', $(this).attr('href'));
    e.preventDefault(); // デフォルトの挙動をキャンセル
  });
});
</script>
</body>
</html>
```
先述のように、`error`イベントはjQuery 3で削除されていますが、jQuery Migrationを利用することで、1.x／2.xと同じく、正しく動作していることが確認できます。ちなみに、太字のコードをコメントアウト（もしくは削除）すると、コードは正しく動作しない――エラーとなることが確認できます。

## 非推奨コードを検出する
このように、jQuery Migrationを利用することで、気軽にjQuery3を導入できますが、もちろん、これはあくまで一時的な措置にすぎません。基本的には、非推奨／削除となったコードは修正し、純粋にjquery3が提供している機能だけでアプリが動作していくようにすべきでしょう。  

そして、非推奨／削除されたメソッドを利用しているコードを検出するためにも、jQuery Migrationは利用できます。ただし、その場合、Production版ではなく、Development版を利用してください。先ほどのリストであれば、太字の部分を以下のように書き換えます（「.min.js」を「.js」に）。

```html
<script src="https://code.jquery.com/jquery-migrate-3.0.0.js"></script>
```

これによって、ブラウザーの開発者ツール（コンソール）に問題となるコードを警告ログとして出力できます。先のサンプルの場合は、「JQMIGRATE: jQuery.fn.error() is deprecated」と表示されます。ログと、その詳細については、以下のページも参照してください。

[jQuery Migrate Plugin - Warning Messages［Branch: master］（英語）](https://github.com/jquery/jquery-migrate/blob/master/warnings.md)

あとは、ログを見ながら問題となるコードを修正していくことで、効率的にjQuery 3に対応できます。警告が出なくなったら、jQuery Migrationを外して、jQuery 3単体でも問題なくコードが動作することを確認しておきましょう。

## より古いコードのために
ただし、jQuery 3向けのjQuery Migrationは、1.11.0／2.1.0以前のjQueryには対応していません。その場合は、いったん、jQuery 1.x／2.x系の最新版（執筆時点では1.12.4、2.2.4）に対応するよう、コードを修正していきましょう。  

これには、jQuery 1.x／2.xの最新版を導入した上で、jQuery 1.x／2.x向けのjQuery Migration（Development版）を適用します。  

```html
<script src="https://code.jquery.com/jquery-1.12.4.min.js"></script>
<script src="https://code.jquery.com/jquery-migrate-1.4.1.js"></script>
```

あとは警告ログが出なくなるまで、既存のコードを修正していきます。警告ログに関する詳細は、以下のページも参照してください。

[jQuery Migrate Plugin - Warning Messages［Branch: 1.x-stable］（英語）](https://github.com/jquery/jquery-migrate/blob/1.x-stable/warnings.md)

修正が完了したら、jQuery Migrationを外してアプリが問題なく動作することを確認します。これでjQuery 1.x／2.xの最新版への対応ができたはずなので、あとは、前項の手順に沿って、jQuery 3への対応を進めます。

手順として表すと面倒にも感じるかもしれませんが、jQuery 3への移行は一般的にはそれほど難しいことではありません。積極的に移行を進めていきましょう！
