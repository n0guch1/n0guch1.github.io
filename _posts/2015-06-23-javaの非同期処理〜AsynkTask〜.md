---
layout: style
title: javaの非同期処理 〜AsynkTask〜
category: android
summary: 非同期処理の基本的な使い方について
---

<h3> 〜AsynkTaskとは〜 </h3>

公式：http://developer.android.com/reference/android/os/AsyncTask.html

Javaでよく使われる非同期処理。
使われる理由としては、

1. 必要に応じて非同期処理と画面更新処理を行うメソッドがある
2. キャンセル処理を行うメソッドがある
3. パラメーターが渡せる
4. 実装が簡単
などがあげられます。
キャンセル処理やメインスレッド処理を非同期処理と組み合わせる場合に、
自前で実装しなくて楽になります。

<h3> 〜主な主要メソッド〜 </h3>

```
・AsyncTask<Params, Progress, Result>

第1引数…メインスレッドから別スレッドに渡すパラメーターの方
※ doInBackground()の引数の型
※ 非同期処理にパラメータを渡したい場合に扱う。特になければnullを指定する

第2引数…進捗度合を表示する時に利用したい型
※ onProgressUpdate()の引数の型
※ 進捗度を計りたい場合に扱う。特になければnullを指定する

第3引数…バックグラウンド処理完了時に受け取る型
※ doInBackground()の戻り値の型
※ onPostExecute()の引数の型
※ 非同期処理からメインスレッドに結果(値)を受け取りたい時に扱う
```

```
・doInBackground()
別スレッドで実行されます。
この処理のみ必ず実装しなければいけません。
```

```
・onPreExecute()
doInBackground実行直前にメインスレッドで処理を行います。
非同期前にメインスレッドで実行したい処理を記述します。
```

```
・onProgressUpdateは()
非同期処理の進行状況をプログレスバーで表示する場合などに扱います。
doInBackgroundのfor文内で呼ぶイメージです
```

```
・onPostExecute()
doInBackground実行直後にメインスレッドで実行されます
doInBackgroundから戻り値で結果を受け取れます
```

※ onPreExecute() >  doInBackground() > onPostExecute() の実行順で呼ばれます
onProgressUpdate()はdoInBackground()内で呼びます

※ 無名メソッドで処理する場合は、上記メソッド内で変数を扱う場合、
final修飾子を付けるかクラス変数(static)に持たせる必要があります。

```
・cancel(boolean)
非同期処理をキャンセルします
```

```
・onCancelled()
キャンセル後に実行されます。
```

<h3> 〜実装コード〜 </h3>

例：取りあえず特にパラメータや戻り値を気にしない場合のAsynkTask

``` java
new AsyncTask<Void, Void, Void>() {
    @Override
    protected Void doInBackground(Void... params) {
                //非同期処理
        return null;
    }
}.execute();
```

例:パラメータ、戻り値、キャンセルを意識したAsyncTask

``` java
AsyncTask<String, Void, Number> task = new AsyncTask<String, Void, Number>() {
    @Override
    protected Number doInBackground(String... params) {
        //非同期処理
        Log.v("tag","パラメータ:"+params[0]);
        int statusCode = 200;
        return statusCode;//このreturnしたオブジェクトがonPostExecute()に渡される
    }
    @Override
    protected void onPostExecute(Number resultCode) {
        //非同期後の処理　メインスレッドでの処理
        Log.v("tag","通信結果:"+resultCode);
    }
    @Override
    protected void onCancelled() {
        //キャンセル処理
        Log.v("tag","キャンセル");
    }
};
//AsyncTask実行
task.execute("POST",null,null);//ここの第一引数がdoInBackground()に渡される

//AsyncTaskキャンセル
task.cancel(true);
```

<h3> 〜非同期待ち〜 </h3>

非同期処理をメインスレッドで待ちたい場合もあります。
例えば単体テストで非同期のテスト結果を待ちたい場合など。

そういう時に便利なのが「CountDownLatch」です。

1.カウントダウン用の引数を渡してインスタンスを生成し、非同期になんらかの形で渡します。

``` java
CountDownLatch latch = new CountDownLatch(1);
```

2.カウントダウンが0になるのを待ちます

``` java
latch.await();
```

3.非同期のコールバック内でカウントします

```java
latch.countDown();//-1
```
これでlatchのプロパティが0になるとawait()後の処理が実行されます。

先ほどのAsynkTaskと組み合わせると以下のような形になります。

```java
//非同期内で実行するためにfinal修飾子をつける
final CountDownLatch latch = new CountDownLatch(1);
new AsyncTask<Void, Void, Void>() {
    @Override
    protected Void doInBackground(Void... params) {
                //非同期処理
        latch.countDown();
        return null;
    }
}.execute();;

//非同期待ち
try {
    latch.await();
} catch (InterruptedException e) {
    e.printStackTrace();
}
Log.v("tag","完了");
```
