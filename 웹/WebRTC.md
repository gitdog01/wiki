# 0221 공부 WebRTC

## WebRTC

WebRTC(Web Real-Time Communication)는 웹 브라우저 간에 플러그인의 도움 없이 서로 통신할 수 있도록 설계된 API이다.

- 웹용 P2P 기술
- 웹 브라우저 간에 플러그인의 도움 없이 서로 통신할 수 있도록 설계된 API
- W3C에서 제시된 초안
- 음성통화, 영상통화, P2P 파일공유 등으로 활용가능

## 장점

- Latency가 짧다.
- 따로 설치할 필요없이 Javascript 에서 작동한다.

## [ICE](https://developer.mozilla.org/ko/docs/Web/API/WebRTC_API/Protocols#ice)

[Interactive Connectivity Establishment (ICE)](http://en.wikipedia.org/wiki/Interactive_Connectivity_Establishment) 는 브라우저가 peer를 통한 연결이 가능하도록 하게 해주는 프레임워크입니다. Peer A에서 Peer B까지 단순하게 연결하는 것으로는 작동하지 않는 것에 대한 이유는 많이 있습니다. 연결을 시도하는 방화벽을 통과해야하기도 하고, 단말에 퍼블릭 IP가 없다면 유일한 주소값을 할당해야할 필요도 있으며 라우터가 peer간의 직접연결을 허용하지 않을 때에는 데이터를 릴레이해야할 경우도 있습니다. ICE는 이러한 작업을 수행하기 위해 STUN과 TURN 서버 둘다 혹은 하나의 서버를 사용합니다.

## [STUN](https://developer.mozilla.org/ko/docs/Web/API/WebRTC_API/Protocols#stun)

[Session Traversal Utilities for **NAT** (STU**N**)](http://en.wikipedia.org/wiki/STUN) (단축어 안의 단축어) 는 당신의 공개 주소(public address)를 발견하거나 peer간의 직접 연결을 막는 등의 라우터의 제한을 결정하는 프로토콜입니다.

클라이언트는 인터넷을 통해 클라이언트의 공개주소와 라우터의 NAT 뒤에 있는 클라이언트가 접근가능한지에 대한 답변을 위한 요청을 STUN서버에 보냅니다.

![image](https://user-images.githubusercontent.com/5876149/220153395-ff8ec6f2-fb4d-45c3-b196-4ce1720b8904.png)


## [NAT](https://developer.mozilla.org/ko/docs/Web/API/WebRTC_API/Protocols#nat)

[Network Address Translation (NAT)](https://en.wikipedia.org/wiki/Network_address_translation) 는 단말에 공개 IP주소를 할당하기 위해 사용됩니다. 라우터는 공개 IP 주소를 갖고 있고 모든 단말들은 라우터에 연결되어 있으며 비공개 IP주소(private IP Address)를 갖고 있습니다. 요청은 단말의 비공개 주소로부터 라우터의 공개 주소와 유일한 포트를 기반으로 번역될 것입니다. 이러한 경유로 각각의 단말이 유일한 공개 IP 없이 인터넷 상에서 검색 될 수 있는 방법입니다.

어떠한 라우터들은 네트워크에 연결할수 있는 제한을 갖고 있습니다. 따라서 STUN서버에 의해 공개 IP주소를 발견한다고 해도 모두가 연결을 할수 있다는 것은 아닙니다. 이를 위해 TURN이 필요합니다.

## [TURN](https://developer.mozilla.org/ko/docs/Web/API/WebRTC_API/Protocols#turn)

몇몇의 라우터들은 Symmetric NAT이라고 불리우는 제한을 위한 NAT을 채용하고 있습니다. 이 말은 peer들이 오직 이전에 연결한 적 있는 연결들만 허용한다는 것입니다.

[Traversal Using Relays around NAT (TURN)](http://en.wikipedia.org/wiki/TURN) 은 TURN 서버와 연결하고 모든 정보를 그 서버에 전달하는 것으로 Symmetric NAT 제한을 우회하는 것을 의미합니다. 이를 위해 TURN 서버와 연결을 한 후 모든 peer들에게 저 서버에 모든 패킷을 보내고 다시 나에게 전달해달라고 해야 합니다. 이것은 명백히 오버헤드가 발생하므로 이 방법은 다른 대안이 없을 경우만 사용하게 됩니다.

![image](https://user-images.githubusercontent.com/5876149/220153434-be95dbbd-c21e-45e1-b7cf-363a7df540c4.png)

## 시그널링

서로 다른 네트워크에 있는 2개의 디바이스들을 통신하기 위해서는, 각 디바이스들의 위치(IP) 발견 및 미디어 포맷협의가 필요합니다. 이 프로세스를 **시그널링 signaling**
 이라 부르고 각 디바이스들을 상호간에 동의된 서버(socket.io 혹은 websocket을 이용한 서버)에 연결시킵니다.

## ToDo

- 나만의 시그널링 서버 만들어보기 ??..
- [https://doublem.org/webrtc-story-02/](https://doublem.org/webrtc-story-02/)
- [https://medium.com/monday-9-pm/초보개발자-webrtc-시그널링서버-만들기-caf8cf9adc9a](https://medium.com/monday-9-pm/%EC%B4%88%EB%B3%B4%EA%B0%9C%EB%B0%9C%EC%9E%90-webrtc-%EC%8B%9C%EA%B7%B8%EB%84%90%EB%A7%81%EC%84%9C%EB%B2%84-%EB%A7%8C%EB%93%A4%EA%B8%B0-caf8cf9adc9a)

---

출처 

- [https://zetawiki.com/wiki/WebRTC](https://zetawiki.com/wiki/WebRTC)
- [https://ko.wikipedia.org/wiki/WebRTC](https://ko.wikipedia.org/wiki/WebRTC)
- [https://web.dev/webrtc-infrastructure/](https://web.dev/webrtc-infrastructure/)
- [https://gh402.tistory.com/38](https://gh402.tistory.com/38)
- [https://developer.mozilla.org/ko/docs/Web/API/WebRTC_API/Protocols](https://developer.mozilla.org/ko/docs/Web/API/WebRTC_API/Protocols)
- [https://velog.io/@kkj53051000/WebRTC와-시그널링이란-시그널링-Mozilla-분석](https://velog.io/@kkj53051000/WebRTC%EC%99%80-%EC%8B%9C%EA%B7%B8%EB%84%90%EB%A7%81%EC%9D%B4%EB%9E%80-%EC%8B%9C%EA%B7%B8%EB%84%90%EB%A7%81-Mozilla-%EB%B6%84%EC%84%9D)
