---
layout: style
title: Atom始めました
---

<h3> 〜Atomとは〜 </h3>

プログラミング用(開発者用)のテキストエディタです。
vimをGUI上で操作できるイメージです。vim同様様々な拡張プラグインが存在するので、
自分のスタイルに合わせたエディタを作成可能。また標準でMarkdownプレビュー機能を備えているのでwindowsのMarkdownプレビュー用エディタとしても便利です。

<h3> 〜Atomのインストール〜 </h3>
[Atomインストール](https://github.com/atom/atom/releases)からOSに合わせて取得。
以下Macまたはwindowsの対応.zip
Mac：atom-mac.zip
Windows：atom-windows.zip

<h3> 〜インストール後にやったこと〜 </h3>

・社内などでプロキシがある場合は突破する
1. 「/userhome/.atom」 に 「apmrc」ファイル作成
2. 「https-proxy = https://ホスト:ポート番号」を記述
3. 「control(macはcommand) + , 」を押してpackagesで適当なパッケージを検索できたら完了

・japanese-wrapパッケージを入れる
日本語を折り返し表示してくれる。デフォルトは改行が入らないと横にずらずらとなってしまいます。

・open-recentパッケージを入れる
開いたフォルダ、ファイルの履歴を保存し、すぐに開ける。

・Term2パッケージを入れる
Atom上でターミナルを開けるのですぐ「gekyl server」や「git push」
などコマンド入力が可能になる

・タブをスペース4つ分に変更
「control(macはcommand) + , 」のTab Lengthを4に変更
デフォルトのタブスペースが2つだった・・・

・SaveSrttionパッケージを入れる
前回のセッション(開いていた状態)からスタートに変更する
便利でしたが、毎回前回の開いていた状態からになるので好みがありそう

・等幅フォントに変更
フォントが中華フォントなので等幅フォントに変更する
「上記タブAtom → Open your stylesheet」
表示されたstyles.leesの内容を以下に変更

    /*
    * Your Stylesheet
    *
    * This stylesheet is loaded when Atom starts up and is reloaded automatically
    * when it is changed.
    *
    * If you are unfamiliar with LESS, you can read more about it here:
    * http://www.lesscss.org
    */

    .tree-view {
        font-family: "Meiryo";
        font-size: 14px;
    }

    atom-text-editor {
        font-family: "Osaka-Mono";
    }

    atom-text-editor .cursor {
    }

    .markdown-preview { font-family: "Meiryo"; h1, h2, h3, h4, h5, h6 { font-family: "Meiryo"; } }


<h3> 〜感想〜 </h3>

デフォルトのままでも十分使えたので非常にとっつきやすい印象でした。
インストール後面倒な設定も特になく使えたのが好印象です。
さらに使い慣れれば開発速度が確然と上がるのは間違いないでしょう。
