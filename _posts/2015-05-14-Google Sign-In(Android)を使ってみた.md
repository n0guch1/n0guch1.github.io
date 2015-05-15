---
layout: style
title: Google Sign-In(Android)を使ってみた
---

今回実装したコードは一番下に乗せてあります。

<h3> 〜Google Sign-Inとは〜 </h3>

公式:[Google Sign-In](https://developers.google.com/identity/sign-in/android/)
Google認証をライブラリを使って手軽に行うことが出来ます。
認証を行うことでGoogleが提供するAPI(Google DriveやGoogle Mapsなど)を利用することが出来ます。

<h3> 〜手順1 セットアップ〜 </h3>

公式手順1:[Start integrating Google Sign-In into your Android app](https://developers.google.com/identity/sign-in/android/getting-started)
日本語記事:[Google Sign-In](http://qiita.com/Yuki_312/items/437f41aa7a37a9009c25)

公式手順に沿って実装して行きます。
大まかな流れは以下の通りになります。

1. [Google Developers Console. ](https://console.developers.google.com/project)でプロジェクト作成&設定
2. Android Studio で対象のプロジェクトに「Google play Services」の導入
3. GoogleApiClientの初期化になります

◆詰まりポイント
1.の場合
証明書の取得でAndroidStudioの場合は以下のコマンドをターミナルに入れる
「keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore -list -v」

3.の場合
 [GoogleApiClientについて ](http://qiita.com/inuko/items/ea08b26f3429758ced72)


<h3> 〜手順2 Google認証実装〜 </h3>

公式手順2：[Google Sign-In for Android](https://developers.google.com/identity/sign-in/android/sign-in)

セットアップが終わったところで、メイン機能の肉付けに入ります。
大まかな流れは以下の通りになります。

1. SignInButtonを作成
2. SignOutButtonを作成
3. 認証失敗時/成功時の機能を作成 [ここ](https://developers.google.com/identity/sign-in/android/sign-in#add_the_google_sign-in_button_to_your_app)の1~6まで実装

◆詰まりポイント
1.の場合
デフォルトのボタンデザインがGoogle+の物だった。
その他ボタンレイアウトは素材をダウンロードして自前で実装します。
自前で実装する場合はGoogleの規約にそって実装しないといけないらしいです。
公式：[ボタンレイアウト](https://developers.google.com/identity/branding-guidelines#sign-in-button)

<h3> 〜手順3 AuthToken,UserIdの取得〜 </h3>

公式手順3：[Getting profile information](https://developers.google.com/identity/sign-in/android/people#retrieve_the_email_address_of_a_signed-in_user)

認証が完了したところで、AuthToken及びUserId(メールアドレス)の取得に移ります。
大まかな流れは以下の通りになります。

1. UserIdを取得
2. 取得したUserIdを元にAuthTokenを取得

◆詰まりポイント
1.の場合
公式のUserIdの取得の仕方がGoogle+を使うため、
Googleの[コンソール](https://console.developers.google.com/project/onyx-gear-93107/apiui/apis/library)からGoogle+を有効にしなければいけません。
リファレンス：[com.google.android.gms.plus](https://developer.android.com/reference/com/google/android/gms/plus/package-summary.html)

2.の場合
公式に記載が無いので自前で実装
参考：[GoogleAuthUtil実装例](http://d.hatena.ne.jp/satoshis/20141121/p1)
リファレンス：[GoogleAuthUtil](https://developer.android.com/reference/com/google/android/gms/auth/GoogleAuthUtil.html)

<h3> 〜問題点〜 </h3>

Google+APIは使いたくなかったのですが、UserId取得の仕方が思いっきりGoogle+APIを使っていました。


<h3> 〜実装コード〜 </h3>

◆MainActivity.java

``` java

package mb.cloud.nifty.com.googleoauthtest;

import android.accounts.Account;
import android.accounts.AccountManager;
import android.app.Activity;
import android.content.Intent;
import android.content.IntentSender.SendIntentException;
import android.os.AsyncTask;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import com.google.android.gms.auth.GoogleAuthException;
import com.google.android.gms.auth.GoogleAuthUtil;
import com.google.android.gms.auth.UserRecoverableAuthException;
import com.google.android.gms.common.ConnectionResult;
import com.google.android.gms.common.Scopes;
import com.google.android.gms.common.api.GoogleApiClient;
import com.google.android.gms.common.api.Scope;
import com.google.android.gms.plus.Plus;
import com.google.android.gms.plus.model.people.Person;

import java.io.IOException;

import static android.view.View.VISIBLE;

public class MainActivity extends Activity implements
        GoogleApiClient.ConnectionCallbacks,
        GoogleApiClient.OnConnectionFailedListener,
        View.OnClickListener{

    String accountName;
    String userId;
    String email;
    String authToken;

    //Google play Servicesに接続中フラグ
    private boolean mSignInClicked;


    /**
     * True if we are in the process of resolving a ConnectionResult
     */
    private boolean mIntentInProgress;


    /* Request code used to invoke sign in user interactions. */
    private static final int RC_SIGN_IN = 0;

    /* Client used to interact with Google APIs. */
    private GoogleApiClient mGoogleApiClient;


    @Override
    public void onCreate(Bundle savedInstanceState) {
        Log.v("tag", "実行開始");
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //mGoogleApiClientの初期化
        mGoogleApiClient = new GoogleApiClient.Builder(this)
                .addConnectionCallbacks(this)
                .addOnConnectionFailedListener(this)
                .addApi(Plus.API)
                .addScope(new Scope("profile"))
                .build();


        //認証ボタンのデザイン実装 ※Googleの規約に沿うこと
        Button googleButton = (Button) findViewById(R.id.original_sign_out_button);
        googleButton.setVisibility(VISIBLE);
        googleButton.setBackgroundDrawable(getResources().getDrawable(R.drawable.btn_google_signin_normal));
        //googleButton.setBackground(getResources().getDrawable(com.google.android.gms.R.drawable.common_signin_btn_text_normal_dark));
        //googleButton.getResources().getString(com.google.android.gms.R.string.common_signin_button_text);

        //ボタン登録
        findViewById(R.id.original_sign_out_button).setOnClickListener(this);
        findViewById(R.id.sign_in_button).setOnClickListener(this);
        findViewById(R.id.sign_out_button).setOnClickListener(this);
    }

    public void onClick(View view) {
        //認証ログイン
        if (view.getId() == R.id.sign_in_button && !mGoogleApiClient.isConnecting()) {
            mSignInClicked = true;
            mGoogleApiClient.connect();
        }
        //オリジナルボタンで認証ログイン
        if (view.getId() == R.id.original_sign_out_button && !mGoogleApiClient.isConnecting()) {
            mSignInClicked = true;
            mGoogleApiClient.connect();
        }

        //認証ログアウト
        if (view.getId() == R.id.sign_out_button) {
            //ログイン中であればログアウトする
            if (mGoogleApiClient.isConnected()) {
                mGoogleApiClient.clearDefaultAccountAndReconnect();
            }
        }
    }

    /**
     * アプリ復帰時に未認証であれば接続する
    */
    @Override
    protected void onStart() {
        super.onStart();
        if (!mGoogleApiClient.isConnected()) {
            mGoogleApiClient.connect();
        }
    }

    /*
     * アプリ離脱時にGoogle Play Servicesからの接続を閉じます
     */
    @Override
    protected void onStop() {
        super.onStop();
        mGoogleApiClient.disconnect();
    }


    /*
     * ログインボタンをタップし接続が確立された場合に呼ばれる
     * mSignInClickedフラグをリセットする
     */
    @Override
    protected void onActivityResult(int requestCode, int responseCode, Intent intent) {
        Log.v("tag","Googleに接続中");
        if (requestCode == RC_SIGN_IN) {
            if (responseCode != RESULT_OK) {
                mSignInClicked = false;
            }

            mIntentInProgress = false;

            if (!mGoogleApiClient.isConnected()) {
                //未ログインならば現在の接続を閉じた後に再度接続開始
                mGoogleApiClient.reconnect();
            }
        }
    }

    /**
     * エラーハンドリング
     * 接続が中断された場合に呼ばれる
     * 引数に原因コードが渡される
     */
    @Override
    public void onConnectionSuspended(int cause) {
        Log.v("tag","接続中断");
        if(cause == GoogleApiClient.ConnectionCallbacks.CAUSE_SERVICE_DISCONNECTED){
            //Google Play Servicesから切断
        }else if(cause == GoogleApiClient.ConnectionCallbacks.CAUSE_NETWORK_LOST){
            //インターネットから切断　
        }
        //エラー発生時に未ログインなら再度ログインする
        if (mGoogleApiClient.isConnected()) {
            mGoogleApiClient.connect();
        }
    }

    /**
     * エラーハンドリング
     * ログイン失敗時に呼ばれる
     * アカウントの選択を再びユーザに求める
     */
    @Override
    public void onConnectionFailed(ConnectionResult result) {
        Log.v("tag","認証失敗");
        if (!mIntentInProgress) {

            //ログイン中で接続を失敗した場合は再度ユーザーにアカウントを選択してもらう
            if (mSignInClicked && result.hasResolution()) {
                // The user has already clicked 'sign-in' so we attempt to resolve all
                // errors until the user is signed in, or they cancel.
                try {
                    result.startResolutionForResult(this, RC_SIGN_IN);
                    mIntentInProgress = true;
                } catch (SendIntentException e) {
                    // The intent was canceled before it was sent.  Return to the default
                    // state and attempt to connect to get an updated ConnectionResult.
                    mIntentInProgress = false;
                    mGoogleApiClient.connect();
                }
            }
        }
    }

    /*
     * 認証ログイン成功時に呼ばれる
     */
    @Override
    public void onConnected(Bundle connectionHint) {
        Log.v("tag","認証ログイン成功");
        // We've resolved any connection errors.  mGoogleApiClient can be used to
        // access Google APIs on behalf of the user.
        mSignInClicked = false;
        Toast.makeText(this, "User is connected!", Toast.LENGTH_LONG).show();

        //ログインユーザー情報取得 ※UserID取得部分はGoogle+APIを使う
        if (Plus.PeopleApi.getCurrentPerson(mGoogleApiClient) != null) {
            Person currentPerson = Plus.PeopleApi.getCurrentPerson(mGoogleApiClient);
            String email = Plus.AccountApi.getAccountName(mGoogleApiClient);
            String personName = currentPerson.getDisplayName();

            //UserId取得は以下メソッドでも可能
            //String userId = currentPerson.getId();
            //this.userId = userId;

            this.accountName = personName;
            this.email = email;

            //取得データをviewに反映
            TextView accountNameView = (TextView) findViewById(R.id.accountName);
            accountNameView.setText("【アカウント名】 : "+this.accountName);

            TextView emailView = (TextView) findViewById(R.id.email);
            emailView.setText("【Eメール】 : "+this.email);

            //authTokenの取得
            authToken();
        }
    }

    private static final String scope = "oauth2:"+Scopes.PROFILE;
    static final int REQUEST_AUTHORIZATION = 2;

    /**
     * authTokenを取得する
     */
    private void authToken() {
        AsyncTask<Void, Void, Boolean> task = new AsyncTask<Void, Void, Boolean>() {
            @Override
            protected Boolean doInBackground(Void... params) {
                try {
                    //非推奨
                    //authToken = GoogleAuthUtil.getToken(MainActivity.this, email, scope);

                    AccountManager manager = AccountManager.get(MainActivity.this);
                    Account[] accountArray = manager.getAccountsByType("com.google");
                    for(Account account : accountArray){
                        //ログイン中のアカウントのAuthData取得
                        if(account.name.equals(email)){
                            authToken = GoogleAuthUtil.getToken(MainActivity.this, account, scope);
                            //メインスレッドとは別で実行する
                            userId = GoogleAuthUtil.getAccountId(MainActivity.this, email);
                        }
                    }
                } catch (UserRecoverableAuthException e) {
                    startActivityForResult(e.getIntent(), REQUEST_AUTHORIZATION);
                    Log.v("tag", "UserRecoverableAuthException:" + e);
                    return false;
                } catch (IOException e) {
                    Log.v("tag", "IOException:" + e);
                } catch (GoogleAuthException e) {
                    Log.v("tag", "GoogleAuthException:" + e);
                }

                return true;
            }

            @Override
            protected void onPreExecute() {
                //AuthTokenをViewに反映
                TextView AuthTokenView = (TextView) findViewById(R.id.authToken);
                AuthTokenView.setText("【AuthToken】 : "+authToken);

                //UserIDをViewに反映
                TextView personIdView = (TextView) findViewById(R.id.userId);
                personIdView.setText("【ID】 : "+userId);

                //ログイン情報ログ出力
                Log.v("tag","ID:"+userId);//UserId
                Log.v("tag","アカウント名:"+accountName);//アカウント名
                Log.v("tag","Eメール:"+email);//メールアドレス
                Log.v("tag","AuthToken:"+authToken);//トークン
            }
        };
        task.execute(new Void[0]);
    }

}


```

◆activity_main.xml

``` java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">

    <com.google.android.gms.common.SignInButton
        android:id="@+id/sign_in_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="31dp"
        android:layout_below="@+id/sign_out_button"
        android:layout_alignParentLeft="true"
        android:layout_alignParentStart="true" />

    <Button
        android:id="@+id/sign_out_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="ログアウト"
        android:layout_alignParentTop="true"
        android:layout_alignParentRight="true"
        android:layout_alignParentEnd="true" />

    <Button
        android:id="@+id/original_sign_out_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/common_signin_button_text"
        android:layout_above="@+id/sign_in_button"
        android:layout_alignParentLeft="true"
        android:layout_alignParentStart="true" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:text="アカウント名 : 未ログイン"
        android:id="@+id/accountName"
        android:textSize="10dp"
        android:layout_below="@+id/title"
        android:layout_marginTop="34dp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="ログイン情報"
        android:id="@+id/title"
        android:layout_marginTop="33dp"
        android:layout_below="@+id/sign_in_button"
        android:layout_centerHorizontal="true" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:text="Eメール : 未ログイン"
        android:id="@+id/email"
        android:textSize="10dp"
        android:layout_below="@+id/accountName"
        android:layout_toLeftOf="@+id/title" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:text="AuthToken : 未ログイン"
        android:id="@+id/authToken"
        android:textSize="10dp"
        android:layout_below="@+id/email"
        android:layout_toLeftOf="@+id/title" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:text="UserID : 未ログイン"
        android:id="@+id/userId"
        android:textSize="10dp"
        android:layout_below="@+id/authToken"
        android:layout_toLeftOf="@+id/title" />

</RelativeLayout>

```

◆AndroidManifest.xml

```java
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="mb.cloud.nifty.com.googleoauthtest" >

    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
    <uses-permission android:name="android.permission.USE_CREDENTIALS" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>

```
