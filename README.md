<<<<<<< HEAD
# about this Application
ninkatsu P2Pチャットを行うアプリケーションの Web 版である。

__システム構成__ および __動作概要__ については https://github.com/shirow-ozawa/ninkatsu-P2P を参照。

## 実動サンプル
https://mulodo-japan.github.io/test-p2p/
※ バックエンドで呼び出している STUN/TURNサーバのインスタンスは 2019-11-30 に削除予定。それ以降はサーバを別途用意し、下記手順に従ってサーバURL等の再設定を行わないと正常に動作しない。

## How to start develop
前提: STUN/TURNサーバが設定済みであること。

用意したSTUN/TURNサーバの設定に合わせて turnUrl, turnUserName, turnUserPW を設定する。

## How to Login
https://mulodo-japan.github.io/test-p2p/ (以下、APP_URL）にアクセスする。

下記アカウントのいずれかでログインを行う。パスワードはいずれも `password`  
taro@hoge.com  
hanako@hoge.com  
jiro@fuga.com  
tamako@fuga.com  

なお、  
taro@hoge.com と hanako@hoge.com  
jiro@fuga.com と tamako@fuga.com  
がそれぞれカップルである想定。

以下、 taro@hoge.com が先にログインし hanako@hoge.com が後からログインするものとする。

### ルームの作成（taro）
Firebase Authentication によるユーザ認証が完了すると「ルーム」が作成される。  
ルームの実体は、Firebase Realtime Database のオブジェクト。  

ルームの例: https://ninkatsu-sig-fire.firebaseio.com/ninkatsu-p2p/multi/room/room_-Lq03h5qCthdesrbdZAj

この結果、下記のようにオブジェクトが作成される。
- <ルーム>/<ルームID>/members 以下に `{ key: <taroのUID>, value: true }`
- member/<taroのUID>/rooms 以下に `{ key: <ルームID>, value: true }`

### 入室（hanako）
APP_URL?<ルームID> をブラウザで開く。ルームID は taro がログインした際に表示されるアラートからコピーする。  

### チャットの流れ
1. taro, hanako どちらかが Connect ボタンを押下する。その際、SDP to send, SDP received に 交換された SDP が自動的に表示される。
1. posting_contents にチャットメッセージを書き込み、 Post を押下して投稿する。
1. taro, hanako 両方のウインドウの posted_contents に投稿が表示される。
1. Hang up を押下すると P2P 接続が終了する。
1. Sign up を押下すると Firebase Authentication のログイン状態が「ログアウト」になる。

### 同一マシンで検証を行う場合の注意点
一度ログインを行うと __明示的にログアウトを行わない限りログイン状態が継続する__ (*1) ので、同一マシン上で動作確認を行う場合は下記のようにする必要がある。
- __別のブラウザ__ （例：Chrome と Firefox）を立ち上げる
- __異なる Google アカウントでログインしたウインドウを開く__（Chrome の場合）

## TODO
* Sign Out を行った後ルーム削除する処理の実装

## 実装メモ
### 画像の送信
JavaScript の FileAPI を使用してファイルをアップロード  
-> HTML5 の Canvas オブジェクトに描画  
-> Canvas から Blob オブジェクトを作成  
-> Blob オブジェクトをBASE64エンコードした文字列として送信する。

*1 firebase.auth().setPersistence で変更可能。詳細は https://firebase.google.com/docs/auth/web/auth-state-persistence?hl=ja 参照。
