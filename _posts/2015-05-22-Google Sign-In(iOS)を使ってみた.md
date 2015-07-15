---
layout: style
title: Google Sign-In(iOS)を使ってみた
category: ios
summary: Google認証のiOS版の使い方について
---

今回実装したコードは一番下に乗せてあります。

<h3> 〜Google Sign-Inとは〜 </h3>

公式:[Google Sign-In(iOS)](https://developers.google.com/identity/sign-in/ios/)
前回の記事で紹介したので割愛します。

<h3> 〜手順1 セットアップ〜 </h3>

公式手順1:[Start integrating Google Sign-In into your iOS app](https://developers.google.com/identity/sign-in/ios/getting-started)

公式手順に沿って実装して行きます。
大まかな流れは以下の通りになります。

1. [Google Developers Console. ](https://console.developers.google.com/project)でプロジェクト作成&設定
2. Xcode で対象のプロジェクトの設定

◆詰まりポイント
2.の場合
・「GoogleSignIn.bundle」を <span style="color:red">**Build Phases > Copy Bundle Resources**</span> に追加すること
・ <span style="color:red">**Info > URL Types**</span> に 二つのURL Typesを追加すること
→例 1つ目:
Identifierに「クライアントキー.apps.googleusercontent.com」(デベロッパーコンソールから取得)
URK Schemesに「com.googleusercontent.apps.クライアントキー」
→例 2つ目:
Identifierに「iOSSDL」(任意の文字列)
URK Schemesに「com.push.googleAuthTest」(アプリのBundle Identifier)

<h3> 〜手順2 Google認証実装〜 </h3>

公式手順2：[Google Sign-In for iOS](https://developers.google.com/identity/sign-in/ios/sign-in)

セットアップが終わったところで、実装コードの導入になります。
大まかな流れは以下の通りになります。

1. SignInButtonを作成
2. SignOutButtonを作成
3. 認証失敗時/成功時の機能を作成

◆詰まりポイント
1.の場合
・StoryBoardにSignInButtonを追加する時は「Button」ではなく「View」を追加する
→そしてViewのCustomClaussのClassに「GIDSignInButton」を設定する
・実機ビルド時にボタンが実機の画面に収まる様配置すること

<h3> 〜手順3 AuthToken,UserIdの取得〜 </h3>

公式手順3：[Getting profile information](https://developers.google.com/identity/sign-in/ios/people)


1. 認証失敗時/成功時のメソッドでIDとトークンを取得

◆詰まりポイント
特になし

<h3> 〜実装コード〜 </h3>

◆ViewController.h

``` objective-c

#import <UIKit/UIKit.h>
#import <GoogleSignIn/GoogleSignIn.h>


@interface ViewController : UIViewController <GIDSignInDelegate>

//デフォルトデザインのサインボタン
@property(weak, nonatomic) IBOutlet GIDSignInButton *signInButton;

//オリジナルのサインアウトボタン
- (IBAction)signOutButton:(UIButton *)sender;

//オリジナルのサインインボタン
- (IBAction)otiginalSignInButton:(UIButton *)sender;

@end


```

◆ViewController.m

``` objective-c

#import "ViewController.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    //Google認証初期化
    //[GIDSignIn sharedInstance].clientID = @"370894706694-8kknc502pnlncbrnoone3q8fhc55vq66.apps.googleusercontent.com";
    GIDSignIn *signInManager = [GIDSignIn sharedInstance];
    signInManager.clientID = @"370894706694-8kknc502pnlncbrnoone3q8fhc55vq66.apps.googleusercontent.com";
    signInManager.delegate = self;//コールバック設定
    // Do any additional setup after loading the view, typically from a nib.
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

//ログイン時のコールバック
- (void)signIn:(GIDSignIn *)signIn
didSignInForUser:(GIDGoogleUser *)user
     withError:(NSError *)error {
    if(error==nil){
    // Perform any operations on signed in user here.
    //self.statusField.text = @"Signed in user";
    NSLog(@"ログイン成功");
    NSLog(@"Id:%@",user.userID);
    GIDAuthentication *authData = user.authentication;
    NSLog(@"accessToken:%@",authData.accessToken);
    }else{
        NSLog(@"ログイン失敗:%@",error);
    }
}

//[[GIDSignIn sharedInstance] disconnect]用コールバック
- (void)signIn:(GIDSignIn *)signIn
didDisconnectWithUser:(GIDGoogleUser *)user
     withError:(NSError *)error {
    // Perform any operations when the user disconnects from app here.
    //self.statusField.text = @"Disconnected user";
}


- (IBAction)signOutButton:(UIButton *)sender {
    NSLog(@"ログアウト実行");
     //[[GIDSignIn sharedInstance] disconnect];
    [[GIDSignIn sharedInstance] signOut];
}

- (IBAction)otiginalSignInButton:(UIButton *)sender {
    NSLog(@"ログイン実行");
    [[GIDSignIn sharedInstance] signIn];
}

@end

```
