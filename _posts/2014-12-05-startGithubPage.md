---
layout: style
title: GithubPage作成
category: ブログ
summary: Github.ioでのブログの作り方について
---

<h3> 〜作成手順〜 </h3>

[ここ](https://help.github.com/articles/creating-pages-with-the-automatic-generator/)を読めば大丈夫!
と思っていましたが何点か躓いたのでポイントをまとめます。

1. Githubアカウントを作成
2. ブランチを作成。ブランチ名は「アカウント名.github.io」
3. AutoMatic page generator でページ作成
4. ローカルに作成したブランチをクローン
5. postフォルダを作成し記事にする.mdファイルを作成
6. 一旦pushして確認。URL「http://ユーザー名.github.io/ファイル名」<br>
※ファイル名に「-」が入っている場合は「/」に置き換える。<br>
※ ビルドに失敗した場合はメールでビルド失敗の内容が届くので対処する<br>
※「title: GithubPage作成」で「:」の後には半角スペース必須
7. layoutsがないのでindexからレイアウトをそのまま拝借し、style.html作成
8.  元々あるindex.htmlを修正


<h3> 〜jekyllインストール〜 </h3>
マークダウンを書くときにはjekyllをインストールしておくと便利。
pushせずにローカルにサーバーを立てて簡単に作成した記事を確認できる!

1. コマンドでjekyllをインストール「sudo gem install jekyll」
2. ymlファイルを作成
3. ymlのあるディレクトリで「jekyll server」実行
4. 表示された「serverAdress」にアクセスして確認
