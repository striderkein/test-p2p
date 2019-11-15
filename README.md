# 妊活P2Pについて

## 実動サンプル
<strike>https://shirow-ozawa.github.io/test-p2p/</strike>
https://mulodo-japan.github.io/test-p2p/

## WebRTC P2P 概要
RTCDataChannel を使用して文字列、オブジェクトの送受信を行う。

## 要件の検証
- 文字列は送信可能か -> 可能
- 画像は送信可能か   -> 可能。方法は 実装メモ 参照。
- NAT 越えは可能か -> 可能。STUN サーバを用意して使用する（後述）。
- Firewall 越えは可能か -> 可能。TURN サーバを用意して使用する（後述）。

## 結論
- 下記すべての SSL/TLS 化が必要。
  - アプリ（Web）サーバ
  - シグナリングサーバ
  - TURN サーバ（※ 必須ではないが事実上必須）
- <strike>video または audio へのアクセスが必須で、それについてのユーザによる許可を得る必要がある</strike>
<strike>
根拠: https://developer.mozilla.org/ja/docs/Web/API/MediaDevices/getUserMedia
</strike>

## 詳細
ここ全部書き直し。
<strike>
NAT 越えおよび Firewall 越えのためには ICE candidate を送信する必要がある
-> ICE candidate は、RTCPeerConnection オブジェクトのコールバック関数でしか収集・送信されない
-> RTCPeerConnection オブジェクトのコールバック関数が呼ばれるためには、同オブジェクトに何らかのメディアストリームを追加する必要がある
-> メディアストリームを取得するためには、プロトコルが HTTPS である必要がある（ブラウザの実装）
-> 間違いでした。正しくは、「NAT や Firewall 下にある端末の接続情報を取得するには Vanilla ICE を使用する必要がある」でした。</strike>

## 課題
* どうやって同じルームに入室するか？  
  後からサインアップしたユーザにルーム名を通知する手段をどうするか？  
  ↓これが使えそう（というかこれしかない気すらする）  
  "Firebase Dynamic Links": https://firebase.google.com/docs/dynamic-links?hl=ja

## 実装メモ
### server-info
* シグナリングサーバ(using Firebase Realtime Database)  
  https://ninkatsu-sig-fire.firebaseio.com
* アプリサーバ(というか GitHub Pages)  
  https://shirow-ozawa.github.io/test-p2p/
* TURN サーバ  
  ec2-13-115-2-96.ap-northeast-1.compute.amazonaws.com    elasticIP使用  

### 画像の送信
  JavaScript の FileAPI を使用してファイルをアップロード
	-> HTML5 の Canvas オブジェクトに描画
  -> Canvas から Blob オブジェクトを作成
  -> Blob オブジェクトをBASE64エンコードした文字列として送信する。

### domain
freenom を使用して下記のドメインを取得（無料）
ninkatsu-p2p.ga
ninkatsu-sig.ga
