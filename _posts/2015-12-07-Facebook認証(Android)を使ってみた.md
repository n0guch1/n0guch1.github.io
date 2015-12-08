---
layout: style
title: Facebook認証(Android)を使ってみた
category: android
summary: FacebookSDKのAndroid版の使い方について

---

今回実装したコードは一番下に乗せてあります。

<h3> 〜Facebook認証とは〜 </h3>

公式:[Facebook Login for Android](https://developers.facebook.com/docs/facebook-login/android)
Facebook認証をライブラリを使って手軽に行うことが出来ます。
認証を行うことでFacebookが提供するAPIを利用することが出来ます。

<h3> 〜前半 セットアップ〜 </h3>

公式手順:[Getting Started Android SDK](https://developers.facebook.com/docs/android/getting-started)

公式手順に沿って実装して行きます。
前半は公式の「Getting Started Android SDK」通りに進めて後半は「Facebook Login for Android」を参考に進めていきます。
「Getting Started Android SDK」の大まかな流れは以下の通りになります。

1. Projectの最小SDKバージョンを「API15(Android 4.0.3)」に設定します
2. Projectの「build.gradle」の「repositories」に「mavenCentral()」を追記します
3. Moduleの「build.gradle」の「dependencies」に「compile 'com.facebook.android:facebook-android-sdk:4.7.0'」を追記します※最新verは公式サイト参照
4. developerサイトで登録したアプリにパッケージ名を設定していない場合は設定する
5. developerサイトで登録したアプリにkeyHashesを設定していない場合は設定する
6. AndroidManifestの設定(activityの登録,ApplicationIdの設定など)

その他参考:[Quick Start for Android](https://developers.facebook.com/quickstarts/?platform=android)


* 詰まりポイント
 * developerサイトで登録したアプリのパッケージ名と同様のパッケージ名をProjectで設定すること
 * keyHashesを取得するコマンドは以下の通り

```
//mac
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64

//windows
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```


<h3> 〜後半 Facebook認証の実装〜 </h3>

公式手順2：[Facebook Login for Android](https://developers.facebook.com/docs/facebook-login/android)

セットアップが終わったところで、メイン機能の実装に入ります。
「Facebook Login for Android」の大まかな流れは以下の通りになります。

1. .xmlにLoginButtonを実装
2. FacebookSDKの初期化(sdkInitialize)を実装
3. 登録後のコールバック(registerCallback)を実装
4. ログイン画面から結果の受け取り(onActivityResult)を実装

* 詰まりポイント
 * sdkInitializeはsetContentViewでxmlを読み込む前に実装すること

<h3> 〜実装コード〜 </h3>

* MainActivity.java

``` java
package example.com.facebooksample;

import android.content.Intent;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;

import com.facebook.CallbackManager;
import com.facebook.FacebookCallback;
import com.facebook.FacebookException;
import com.facebook.FacebookSdk;
import com.facebook.appevents.AppEventsLogger;
import com.facebook.login.LoginManager;
import com.facebook.login.LoginResult;

public class MainActivity extends AppCompatActivity {
    CallbackManager callbackManager;

    @Override
    protected void onCreate(final Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        FacebookSdk.sdkInitialize(getApplicationContext());
        setContentView(R.layout.activity_main);

        callbackManager = CallbackManager.Factory.create();
        LoginManager.getInstance().registerCallback(callbackManager,
                new FacebookCallback<LoginResult>() {
                    @Override
                    public void onSuccess(LoginResult loginResult) {
                        // App code
                        Log.d("tag", "onSuccess");
                        Log.d("tag", "Token:" + loginResult.getAccessToken().getToken());
                        Log.d("tag", "UserId:" + loginResult.getAccessToken().getUserId());
                        Log.d("tag", "Expires:" + loginResult.getAccessToken().getExpires());
                    }

                    @Override
                    public void onCancel() {
                        // App code
                        Log.d("tag", "onCancel");
                    }

                    @Override
                    public void onError(FacebookException exception) {
                        // App code
                        Log.d("tag", "onError:" + exception);
                    }
                });
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        callbackManager.onActivityResult(requestCode, resultCode, data);
    }

    @Override
    protected void onResume() {
        super.onResume();

        // Logs 'install' and 'app activate' App Events.
        AppEventsLogger.activateApp(this);
    }

    @Override
    protected void onPause() {
        super.onPause();

        // Logs 'app deactivate' App Event.
        AppEventsLogger.deactivateApp(this);
    }
}
```

* activity_main.xml

``` xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">

    <com.facebook.login.widget.LoginButton
        android:id="@+id/login_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:layout_gravity="center_horizontal"
        android:layout_marginBottom="30dp"
        android:layout_marginTop="30dp" />

</RelativeLayout>
```

* AndroidManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="example.com.facebooksample" >

    <uses-permission android:name="android.permission.INTERNET"/>

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme" >

        <activity android:name=".MainActivity" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <!-- facebook認証 -->
        <meta-data android:name="com.facebook.sdk.ApplicationId" android:value="@string/facebook_app_id"/>

        <activity android:name="com.facebook.FacebookActivity"
            android:configChanges=
                "keyboard|keyboardHidden|screenLayout|screenSize|orientation"
            android:theme="@android:style/Theme.Translucent.NoTitleBar"
            android:label="@string/app_name" />

        <provider android:authorities="com.facebook.app.FacebookContentProvider1234"
            android:name="com.facebook.FacebookContentProvider"
            android:exported="true" />
        <!-- facebook認証 -->

    </application>

</manifest>
```

* strings.xml

```java
<resources>
    <string name="app_name">FacebookSample</string>
    <string name="facebook_app_id">xxxxxxxxxxxxxxxxx</string>
</resources>
```
