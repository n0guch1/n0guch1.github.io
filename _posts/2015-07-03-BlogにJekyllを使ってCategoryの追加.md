---
layout: style
title: BlogにJekyllを使ってCategoryの追加
category: blog
summary: BlogにCategoryを追加する方法について
---

※この記事はjekyllが入っている前提です。
入れていない場合[一番最初の記事を参照。](http://n0guch1.github.io/blog/2014/12/05/startGithubPage.html)

<h3> 〜カテゴリーとは〜 </h3>
よくブログ記事のサイドメニューなどで見かけるやつです。
見たいカテゴリーをクリックするとそのカテゴリーの一覧が見れます。
記事が溜まってきたのと将来性を考えて今のうちに対応してみます。

<h3> 〜jekyllにおけるプラグインインストールについて〜 </h3>
jekyllのプラグインのインストールは_pluginsフォルダに.rbファイルを置くだけです。
置いておけば後はjekyllが読み込み実行してくれます。

<h3> 〜作成手順〜 </h3>

参考記事(2章のみ):[カテゴリアーカイブ用プラグイン](http://whiskers.nukos.kitchen/2015/01/31/jekyll-archives.html)

1. まずは`「_plugins」`フォルダ(なければ新規作成)に以下のファイルを作成します
<https://github.com/n0guch1/n0guch1.github.io/blob/master/_plugins/category_archive.rb>
このプラグインが「category:」毎のフォルダを動的に作成しその中にカテゴリ毎の一覧ページに必要な情報を作成します


2. 次に`「_layouts」`フォルダ内に以下のファイルを作成します
<https://github.com/n0guch1/n0guch1.github.io/blob/master/_layouts/category_archive.html>
このhtmlで上記プラグインで作った情報を元に記事の一覧ページを作成します

3. 次にカテゴリー一覧の表示をします。表示する場所は任意です。
自分の場合はcssにタグを一つ追加して再度メニューとして表示しました
https://github.com/n0guch1/n0guch1.github.io/blob/master/_layouts/style.html
<!-- カテゴリータグの追加 -->の部分を参照

4. 最後に_config.ymlファイルに以下を追記します
プラグインで使用する定義を記載します

```
# category archive
category_title_prefix: の記事一覧
category_title_suffix: ' '
category_dir: category
```

◆詰まりポイント
* コピペで動かない部分があったのでデバッグして修正しました
* カテゴリー一覧表示のサンプルが無かったので適当に作成しました
* titleに濁音を含めるとリンカーエラー謎
* パスによってはcategoryフォルダをルートディレクトリにコピペする(Github.io未対応のため)

<h3> 〜参考〜 </h3>
* [jekyllでタグ・カテゴリ・マンスリーアーカイブページを作る(二章のみ)](http://whiskers.nukos.kitchen/2015/01/31/jekyll-archives.html)
* [最初にインストールした Jekyll プラグイン(カテゴリページ参照)](http://tech.toshiya240.com/articles/2013/01/jekyll-plugins/)
