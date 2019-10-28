# 妊活P2Pについて

## sample
https://mulodo-japan.github.io/test-p2p/

## WebRTC P2P 概要
RTCDataChannel を使用した P2P 通信で「文字列」および「画像」をやり取りすることができる。

## システム構成
- WWW サーバ: アプリをホストする。URL: https://mulodo-japan.github.io/test-p2p/
- STUN サーバ: NAT 越えのために必要。(TURNサーバと兼用）URL: stun://ec2-13-115-2-96.ap-northeast-1.compute.amazonaws.com
- TURN サーバ: Firewall 越えのために必要。URL: turn://ec2-13-115-2-96.ap-northeast-1.compute.amazonaws.com
- シグナリングサーバ: https://ninkatsu-sig-fire.firebaseio.com/

## 詳細
NAT 越えおよび Firewall 越えのためには ICE candidate を送信する必要がある
-> ICE candidate は、RTCPeerConnection オブジェクトのコールバック関数でしか収集・送信されない
<strike>-> RTCPeerConnection オブジェクトのコールバック関数が呼ばれるためには、同オブジェクトに何らかのメディアストリームを追加する必要がある</strike>
<strike>-> メディアストリームを取得するためには、プロトコルが HTTPS である必要がある（ブラウザの実装）</strike>
-> 間違いでした。正しくは、「NAT や Firewall 下にある端末の接続情報を取得するには Vanilla ICE を使用する必要がある」でした。
