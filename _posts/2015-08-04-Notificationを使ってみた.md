---
layout: style
title: Notificationを使ってみた
category: android
summary: Androidでステータスバーに通知を表示する方法について
---

### 〜Notificationとは〜
Androidは画面上部にステータスバー(通知領域,通知バー)という領域があり、
Notificationクラスを使うことでこの領域にメッセージを表示することができます。

公式リファレンス

* [Notification](http://developer.android.com/reference/android/app/Notification.html)
* [Notification.Builder](http://developer.android.com/reference/android/app/Notification.Builder.html)
* [NotificationCompat.Builder](http://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)

### 〜非推奨について〜
古いブログでは非推奨を使ったやり方が散見しているので結果だけ言いますと、
Android4系以上が対象の場合は「NotificationCompat.Builder」を使いましょう。
※2015年8月3日時点

|      クラス      |     対応Ver      |
|:-----------------|-----------------|
|Notification.Builder|Android3.0 (APIレベル11)|
|NotificationCompat.Builder|Android1.6 (APIレベル4)|

どちらのクラスも最終的にNotificationを戻り値にしたメソッドがあるので、
それを利用してNotificationを生成→notifyで表示という流れになります(Notificationのコンストラクタで非推奨があるため)。

上記生成で使用するNotification.BuilderのgetNotification()メソッドがAPIレベル16で非推奨になっています。
かわりに使用するbuild()メソッドを推奨しているのですが、
Android4.0.4で実行出来ないためAndroid4系以上をサポートするアプリの場合は自然にNotificationCompat.Builderを使う事になります。

### 〜実装〜

[]公式サンプルアプリ](https://developers.google.com/cloud-messaging/android/start)

```
git clone https://github.com/googlesamples/google-services.git
```

基本的なnotificationの設定になります。
その他設定項目は[リファレンス参照](http://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)

```java

//通知タップ時に起動するアクティビティ画面の設定
Intent intent = new Intent(this, MainActivity.class);
intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
PendingIntent pendingIntent = PendingIntent.getActivity(this, 0 /* Request code */, intent,
        PendingIntent.FLAG_ONE_SHOT);

//着信音を端末デフォルトで設定
Uri defaultSoundUri= RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(this)
        .setSmallIcon(R.drawable.ic_stat_ic_notification)//ステータスバーに表示するアイコン
        .setContentTitle("GCM Message")
        .setContentText(message)
        .setAutoCancel(true)//タップ時に通知を削除する
        .setSound(defaultSoundUri)//デフォルトサウンド
        .setContentIntent(pendingIntent);//タップ時の起動画面

NotificationManager notificationManager =
        (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);

//0を指定する事でステータスバーに常に最新の通知を表示する。複数表示する際は事なる数値を指定する
notificationManager.notify(0 /* ID of notification */, notificationBuilder.build());
```
