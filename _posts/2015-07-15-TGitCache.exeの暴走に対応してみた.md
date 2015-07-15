---
layout: style
title: TGitCache.exeの暴走に対応してみた
category: git
summary: TGitCache.exeのCPU占有率が25%になる現象を改善する方法について
---

### 〜TGitCache.exe〜
WindowsのGitをUI操作出来る「TortoiseGit」を入れた際に一緒に入ってくる、
TortoiseGitのキャッシュを管理している実行ファイル。
こいつがキャッシュ処理を始めると終わるまでCPU占有率を25%叩き出していたのでこの現象を改善してみる。

### 〜改善手順〜

結論から言うと単純にTortoiseGitのキャッシュをOFFにするだけです。

1. デスクトップやエクスプローラーのフォルダに対して右クリック

2. TortoiseGit → 設定(settings) → アイコンオーバーレイ(Icon Overlays)を選択

3. 状態キャッシュ(Status cache) の無し(None)にチェックを入れる

キャッシュをOFFにするので動きがもっさりすると思っていましたが、
特に問題なく動いていました。
ですがあくまで自己責任です。

### 〜参考〜
* [TGitCache using 25% CPU](https://code.google.com/p/tortoisegit/issues/detail?id=1242)
