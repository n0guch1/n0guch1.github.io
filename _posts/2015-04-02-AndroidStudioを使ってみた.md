---
layout: style
title: AndroidStudioを使ってみた
---


<h3> 〜AndroidStudioとは〜 </h3>

eclipseに変わるAndroidアプリケーション開発環境。
ここ最近のAndroidアプリケーション新規開発案件は、AndroidStudioが多いらしい。
それと公式もeclipseよりもAndroidStudioを押してきている模様。

<h3> 〜インストール〜 </h3>

参考：[ゼロからわかるAndroid Studioインストール手順（Mac版）](http://thinkit.co.jp/story/2015/02/25/5644/page/0/1#h1-2)

1. JDKをインストール
※eclipseを使っていて既にある場合は不要。
しかしverが古い場合はAndroidStudioが動かないので要注意。

2. AndroidStudioをインストール
・http://developer.android.com/intl/ja/sdk/index.html　の［Download  ndroid Studio for Mac］からインストーラーをダウンロード
・ほとんど指示通りに進めて行けばOK
※既にAndroidStudioを入れている場合は、「I want to import ...」を選択すると設定内容を引き継げる。

<h3> 〜実践〜 </h3>
参考：[「Android Studio」でHello World](http://corgi-lab.com/environment/android-studio-hello-world/)

1. 「New Project」を選択
2. アプリ名、保存場所を設定
3. 最小ビルドターゲットAndroid verを設定
4. アクティビティの設定
後はRunするだけでHelloWorldがエミュレータに表示される

<h3> 〜オススメ設定〜 </h3>

1. 行番号を付ける<br>
Menu > Preferences > Editor > Appearance > Show line numbers
2. 自動インポート ※eclipseの様にマウスオーバーライドでimport出来ないのでONにしておく<br>
Menu > Preferences > Editor > Auto Import > Optimize import on the fly<br>
Menu > Preferences > Editor > Auto Import > Add unambiguous on the fly
3. マウスオーバーでJavaDocの表示<br>
Menu > Preferences > Editor > Show quick doc on mouse over
4. ショートカットの変更<br>
Preferences > Keymap<br>
※eclipseのショートカットキーと同じにも出来るが、<br>
結局AndroidStudioになれるために特に変更はしなかった。
4. ショートカットの一覧表示<br>
Help > Default Keymap Reference

<h3> 〜良く使うショートカット〜 </h3>

|      概要      |     コマンド      |
|:---------------|---------------------:|
|自動フォーマット|Command + Option + L|
|定義にジャンプ|Command + B|
|グレップ|Command + Shift + F|
|実行|Control + R|
|デバッグ実行|Control + D|
|JavaDoc自動生成|/** + Enter|

<h3> 〜感想〜 </h3>

eclipseよりシンプルになっていて使いやすかった。
ショートカットやフォルダ構成がeclipseと違うので慣れが必要。
特にショートカットはeclipseと混合する。
