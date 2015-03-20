---
layout: style
title: Kii Cloud SDK使ってみた 〜Android〜
---
<h3> 〜mBaaSとは〜 </h3>

mBaaSとはサーバー構築不要でDBに必要な機能をRestAPIで提供してくれるサービス。
SDKとはJavaやobjective-cと言った各言語でmBaaSの機能を簡単に実装出来るようにしてくれるフレームワーク。(中身はRestAPIを叩いている)

<h3> 〜Kii Cloud とは〜 </h3>

mBaaSを提供している会社の一つで海外展開や会員データを独自のストレージで提供しセキュリティ&レスポンスタイム向上など、他のmBaaSとの差別化を行っている
※普通のmBaaSはユーザー同士が同じ領域を共有して、アクセス制限などをかけている。

<h3> 〜Kii単語まとめ〜 </h3>

KiiのmBaaSを使うためには最低限以下のKii専用単語を理解すること。

| Kii単語           | 説明                     |
| :---:             | :---:                    |
| [Group](http://documentation.kii.com/ja/starts/cloudsdk/cloudoverview/usergroup/)             | ユーザーオブジェクトの集まり(Role)|
| [Bucket](http://documentation.kii.com/ja/starts/cloudsdk/cloudoverview/bucket/)            | オブジェクトの入れ物。フォルダをイメージすると良い(クラス名)|
| [Topic](http://documentation.kii.com/ja/starts/cloudsdk/managing-push-notification/push_kiicloud/push-to-user/)             | プッシュメッセージ送信用チャンネル (チャンネル)|
| [Scope](http://documentation.kii.com/ja/starts/cloudsdk/cloudoverview/bucketscope/)             | 簡単なアクセス制御。さらに細かくアクセス制御する場合はACLで設定する|

<h3> 〜Push通知まとめ〜 </h3>

GCMとJPushによるプッシュ機能を利用できます。
中国ではGCMのプッシュ通知がかなり不安定なので中国で提供しているJPushなるものを使うらしい。
試してみようと登録サイトに行ってみましたが、諸々中国語での説明で信用が出来なかったためGCMのプッシュ通知のみ試してみました。

大きく分けて三つのPush機能を提供していました。
1. [オブジェクトの更新がされたら指定先にプッシュ通知を飛ばす<br>例：ある Bucket 内に新たな Object が作成された際にプッシュ通知を受けるなど](http://documentation.kii.com/ja/starts/cloudsdk/managing-push-notification/push_kiicloud/push-to-app/)
2. [Topicを指定してプッシュ通知を飛ばす<br>例：グループメンバー間でプッシュメッセージを交換するなど](http://documentation.kii.com/ja/starts/cloudsdk/managing-push-notification/push_kiicloud/push-to-user/)
3. [ユーザーを指定してプッシュ通知を飛ばす<br>例：ある特定のユーザーに対して直接アラートを送るなど](http://documentation.kii.com/ja/starts/cloudsdk/managing-push-notification/push_kiicloud/direct-push/)

今回は3番を試してみました。
導入はチュートリアル通りに行けば1時間かからずに実装出来ます。
何通かプッシュ通知を送りましたが速度も早くすぐに届きました。
実装内容はユーザーオブジェクトに端末情報を紐づけているだけでした。


<h3> 〜Good〜 </h3>

- [ユーザーを対象にプッシュ送信が出来る](http://documentation.kii.com/ja/guides/android/managing-push-notification/direct-push/)
- [オブジェクトの値に変更があった時のプッシュ送信が簡単に出来る](http://documentation.kii.com/ja/guides/android/managing-push-notification/push-to-app/)
- [よく使われるアクセス制御をScopeという形で提供している](http://documentation.kii.com/ja/guides/android/securing-data/)
- [セッショントークンの有効期限を変更出来る(デフォルトはほぼ無期限)](http://documentation.kii.com/ja/starts/cloudsdk/managing-users/accesstoken/)
- [別サービスアカウント認証が豊富](http://documentation.kii.com/ja/starts/cloudsdk/managing-users/external/)
- [パスワードの変更が出来るメソッドが提供されている](http://documentation.kii.com/ja/guides/android/managing-users/passwords/)
- [Google Play servicesの導入手順が素晴らしくわかりやすかった](http://documentation.kii.com/ja/samples/push-notifications/push-notifications-android/configure-eclipse/)
- [同期/非同期がスイッチで見れる](http://documentation.kii.com/ja/guides/android/quickstart/adding-kii-push-notification-to-your-application/adding-push-notification-gcm/)

<h3> 〜Bad〜 </h3>
- [きめ細かいエラー処理(好み次第)](http://documentation.kii.com/ja/guides/android/)
<br>→開発者はtry/catchが好きなのだろうか。エラークラスが多い。エラーコードで切り分け出来るようにすれば良いのでは。
- [Javadocが恐ろしいほど見づらい。クラスが多い](http://documentation.kii.com/references/dotnet/storage/latest/KiiCorp.Cloud.Storage/index.html)
<br>→エラークラスだけでもまとめて欲しい
- [eclipseとAndroidStadioの説明が同一ページに記載されている。](http://documentation.kii.com/ja/samples/push-notifications/push-notifications-android/configure-eclipse/)
<br>→eclipseを使う人にとって、もう片方は不要な情報なので、ページを分けるかスイッチで切り替えられると良いと思った
- [Push通知時のNotificationの説明が無い。自分で調べて自前で実装しないといけない。](http://documentation.kii.com/ja/samples/push-notifications/push-notifications-android/implement-initial-code/)
<br>→SDK内で吸収または実装の仕方をドキュメントに記載していても良いと思った
- [コンパネのマニュアルがない。また表示を日本語対応して欲しい](http://documentation.kii.com/ja/guides/)
<br>→マニュアルがなかったので実際に使ってみて覚えた。英語のみなので英語苦手エンジニアにも優しくして欲しい。逆を言うと英語ユーザーに日本語のみの提供も辛いのだろうなと思った。

<h3> 〜感想〜 </h3>

1. KiiCloudを使いこなすために覚えなければいけない「Kii単語」が多すぎるなと思いました。<br>
使いこなせば便利なんだろうけど非常に取っ付きにくい印象でした。
2. Kii単語以外の提供機能やSDK導入方法がほとんど競合と思われるParceと一緒だった。
3. Androidプッシュ通知のチュートリアルも試してみましたがなんとNotification機能の説明はなく自前で実装する事になりました。<br>
プッシュ通知と言ったら「通知が来たらステータスバーに表示されてタップしたら消えてくれる」というのを求めていたのでその辺りの実装が無かったのが残念でした。<br>
Notificationを実装していて思ったのが「タップしたら消える」処理を入れないと通知って消えないんですね・・<br>
4. 思っていた以上にKiiCloudを使ってみた系のブログがなかった。検索にも上位に引っかからないのでそんなに使われていないのだろうか。
5. 基本的にユーザーオブジェクトに紐づけられてく構造だった。
6. ある一定レベル以上の「広く浅く」の機能が揃っていていいなと思った。
7. やっぱり取っ付きにくいのと日本語対応が微妙だったのが気になりました。

結果、今後使うことはないだろうなと思った。
