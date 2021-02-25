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
このように、jQuery Migrationを利用することで、気軽にjQuery3を導入できますが、もちろん
