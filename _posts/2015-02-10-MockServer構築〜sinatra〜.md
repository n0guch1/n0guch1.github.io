---
layout: style
title: MockServer構築 〜sinatra〜
category: その他
summary: モックサーバーを作成するSinatraの使い方について
---

<h3> 〜モックサーバとは〜 </h3>
「モック」とは、テストなどで用意された見せかけのオブジェクトです。
よくいう「モックアップ」は実物とほぼ同様に似せて作られたオブジェクトとになります。
ここで言う「モックサーバー」は実物に似せた見せかけのサーバーになります。

<h3> 〜モックサーバの利点〜 </h3>
開発の規模が大きくなってくるとWebAPIサーバーをたてる事が多々あると思います。
その過程でテストがしたい時、毎度サーバーにアクセスしたりするのが面倒な時や、
まだAPIサーバーが開発途中で検証しづらい時にモックサーバをたてる事で手軽にテストをする事が出来ます。

<h3> 〜Sinatraとは〜 </h3>
RubyによるWebアプリケーションを簡単にかつ素早く作成するためのフレームワークです。
たった数行でローカルにサーバーを立てる事が出来ます。

<h3> 〜インストール〜 </h3>

1.Rubyが必要ですが最近のMacなら標準で2系が入ってるのでインストール部分は割愛します

2.gemを使ってインストール
「gem install sinatra」 をコンソールで実行。環境によっては「sudo gem install sinatra」を実行。

3.Rubyファイルを作成
適当なフォルダに以下の内容で「hello.rb」を作成します。

    require 'sinatra'
    require 'json'

    #urlで受け取った「:object_id」を「params[:object_id]」で返す
    get '/hello/:class_name/:object_id' do
        object = {
            objectId: params[:object_id],
            class_name: params[:class_name],
            Message: "hello!"
        }

        object.to_json
    end

    #bodyが空の場合は400エラーそれ以外はbodyを返す
    post '/hello' do
        body = request.body.read

        if body == ''
            status 400
            else
            body.to_json
        end
    end

4.サーバーをたてる
作成したファイルのディレクトリで「ruby hello.rb」を実行

5.アクセス-get-
ブラウザを立ち上げてURLに「http:///localhost:4567//hello/GetHellow/n0guch1」を入力。
成功した場合は、ブラウザにGetされた結果のJSONデータが表示されます。

6.アクセス-post-
コンソールで「curl -v -d 'hello!!' http://localhost:4567/hello -w "\n%{http_code}\n"」を実行。
成功した場合は、コンソールにPostされたbodyデータとステータスコードが表示されます。
bodyを空で「curl -v -d '' http://localhost:4567/hello -w "\n%{http_code}\n"」実行。
rubyに書いてある判定に引っかかりエラーコード400が返ります。

ちなみに以下のコマンドでも結果は同じになります。
「curl -v -H "Accept: application/json" -H "Content-type: application/json" -X POST -d 'hello!!!' http://localhost:4567/hello -w "\n%{http_code}\n"」

curlオプション解説
-v : リクエストを「>」でレスポンスを「<」で表時してくれる
-H : ヘッダーの追加
-X : メソッド指定
-d : POSTする本体(body)
-w "\n%{http_code}\n" : ステータスコード表示


<h3> 〜機能拡張〜 </h3>
デフォルトのままだと.rbファイルを直接修正しても、サーバーを再度立ち上げ直さないと修正が反映されません。
以下の方法で修正後にブラウザリロードで修正が反映されるようにします。

1. 「gem install sinatra-contrib」を実行。環境によってはsudoを入れる。
2. 修正が反映されたいファイル(ここではhello.rb)に「require 'sinatra/reloader'」を記述

<h3> 〜感想〜 </h3>

かなり手軽にAPIテストを出来たので、APIサーバーにアクセス出来ない or アクセスが大変な時は、
モックサーバーを使ってテストするのが良いです。
