---
layout: style
title: Unity4.x系にてXcode6.x系以上をbuildする方法
category: unity
summary: UnityExceptionの「Launching iOS project via Xcode4 failed. Check editor log for details」に対処する方法について
---

### 〜エラー内容について〜
Unity4系でiOS端末にビルドを実行した際以下のエラーが発生しました。

```
UnityException: Launching iOS project via Xcode4 failed. Check editor log for details
```

Xcode4を通してiOSプロジェクトをビルドしたら失敗しました、と。。。
自分の場合はXcode4ですらないですが(Xcode6でbuild)、ググってみると同様のエラーが多数ありました。
結果から言うとUnity4がリリースされた時のXcodeVerまでしか対応してないよ、と。
まあ確かに。

### 〜修正内容〜

1.以下のファイルに記載されている「DVTPlugInCompatibilityUUIDs」をコピー

```
/Applications/Xcode.app/Contents/Info.plist
```

2.以下のファイルの「DVTPlugInCompatibilityUUIDs」にコピー内容を追加

```
/Applications/Unity/Unity.app/Contents/PlaybackEngines/iOSSupport/Tools/OSX/Unity4XC.xcplugin/Contents/Info.plist
```
※UnityVerが古いとパスが変わります

```
/Applications/Unity/Unity.app/Contents/BuildTargetTools/iPhonePlayer/Unity4XC.xcplugin/Contents/Info.plist
```

3.Unity,Xcodeを再起動
