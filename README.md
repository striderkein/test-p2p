# 妊活P2Pについて

## 実動サンプル
<strike>https://shirow-ozawa.github.io/test-p2p/</strike>
https://mulodo-japan.github.io/test-p2p/

## WebRTC P2P 概要
RTCDataChannel を使用して文字列、オブジェクトの送受信を行う。

## 要件の検証
- 文字列は送信可能か -> 可能
- 画像は送信可能か   -> 可能。方法は下記の通り。
  JavaScript の FileAPI を使用してファイルをアップロード
	-> HTML5 の Canvas オブジェクトに描画
  -> Canvas から Blob オブジェクトを作成
  -> Blob オブジェクトをBASE64エンコードした文字列として送信する。
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
NAT 越えおよび Firewall 越えのためには ICE candidate を送信する必要がある
-> ICE candidate は、RTCPeerConnection オブジェクトのコールバック関数でしか収集・送信されない
<strike>-> RTCPeerConnection オブジェクトのコールバック関数が呼ばれるためには、同オブジェクトに何らかのメディアストリームを追加する必要がある</strike>
<strike>-> メディアストリームを取得するためには、プロトコルが HTTPS である必要がある（ブラウザの実装）</strike>
-> 間違いでした。正しくは、「NAT や Firewall 下にある端末の接続情報を取得するには Vanilla ICE を使用する必要がある」でした。

## 後から分かったこと
DataChannel というものがあり、そちらだとオブジェクトもそのまま送れる。
-> 文字列の送受信のみ、確認済み。

## 実装メモ
### server-info
sig:      ec2-52-68-208-246.ap-northeast-1.compute.amazonaws.com  elastic -> wss://ninkatsu-sig.ga:3001
www, sig: ec2-13-114-195-158.ap-northeast-1.compute.amazonaws.com elastic -> obsoleted
www:      https://shirow-ozawa.github.io/test-p2p/
TURN:     ec2-13-115-2-96.ap-northeast-1.compute.amazonaws.com    elastic

### domain
freenom を使用して下記のドメインを取得（無料）
ninkatsu-p2p.ga
ninkatsu-sig.ga

### これを使った方がいいのかも？？？
[発表]Amazon API GatewayでWebsocketが利用可能: https://aws.amazon.com/jp/blogs/news/announcing-websocket-apis-in-amazon-api-gateway/