# 0212 공부 TLS/SSL

# TLS 와 SSL

오늘은 웹 보안된 것을 확인 하던 중 TLS 1.0 과 TLS 1.1 을 사용하면 안된다는 것을 알아서 하는김에 TLS 와 SSL 에 대해서 추가로 공부해 보았다. ( 현재 브라우저에서는 사용이 막혀있다. )

S**SL (Secure [Socket](http://www.ktword.co.kr/test/view/view.php?m_temp1=280&id=1298) [Layer](http://www.ktword.co.kr/test/view/view.php?m_temp1=1088&id=1468)) 및 TLS ([Transport Layer](http://www.ktword.co.kr/test/view/view.php?m_temp1=361&id=743) [Security](http://www.ktword.co.kr/test/view/view.php?m_temp1=1387&id=826))**  

- 전송계층(L4)상에서 클라이언트,서버에 대한 인증 및 데이터암호화 수행
    - 클라이언트와 서버 양단 간 응용계층 및 TCP전송계층 사이에서 안전한 보안 채널을 형성해 주는 역할을 하는,보안용 프로토콜
- 주요 응용
    - HTTP (HTTPS),FTP (FTPS),TELNET,SMTP,SIP,POP,IMAP 등 에서 사용 가능
    - 주로,웹 브라우저와웹 서버 사이의 안전한보안 채널을 제공하기 위해 많이 사용됨

### 특징

- 클라이언트/서버 기반의프로토콜
    - 클라이언트/서버 두 응용 상대방 간에, 연결 전 동적 협상에 의한보안 연결 수립
- 응용프로그램(어플리케이션) 자체 구현 가능
    - 대부분의 다른보안프로토콜(EAP,IPsec 등)은운영체제 등에 밀접하게 관련됨
- 계층 위치
    - 응용계층 및전송계층 사이에 위치하나,전송계층과 보다 밀접하게 동작 함
    - 소켓 지향적인프로토콜
- 운반용 프로토콜 및 포트번호
    - 운반용프로토콜 :TCP 또는UDP 한편,UDP 상에서도 가능한 버젼은, DTLS (DatagramTransport LayerSecurity)RFC 6347(2012년)가 있음
    - 사용포트 : 응용 마다 다름 HTTPS의 경우에,HTTP를 위한 SSL/TLS보안 터널 형성을 위해,포트번호 443 를 사용

## 기능

- 상호인증
    - 공개키 인증서를 이용하여 서버, 클라이언트의 상호인증
    - 즉,클라이언트/서버 두 응용 간에 상대방에 대한 인증
- 메세지 인증 (메세지무결성)
    - 메세지 인증 코드HMAC에 의한메세지무결성 제공 (HMACMD5,HMACSHA-1 등)
- 메세지 압축
    - 디폴트는Null (즉, 무압축)
        - (압축알고리즘은 미리 정해지지 않고 협상으로 지정 가능)
- 암호화용세션키 생성(대칭키 합의)을 위한키 교환
    - RSA : 두 키(공개 키 및개인 키)가 하나의수 체계를 형성 (서버공개 키 사용)
    - Diffie-Hellman  :Diffie-Hellman프로토콜을 기반으로 한키 교환 방식

![image](https://user-images.githubusercontent.com/5876149/218312079-3238aa38-4704-4a84-ab2f-774b3d00febf.png)

![image](https://user-images.githubusercontent.com/5876149/218312085-926a8330-956f-4e03-8a46-4388d691f839.png)



### 보안

TLS 는 현재 1.0, 1.1, 1.2, 1.3(최신)으로 총 4 개 버전이 존재합니다. 이 중 구 버전인 TLS 1.0, 1.1 은 POODLE 과 BEAST 와 같은 여러 공격에 취약한 것으로 알려져 있습니다.

### **► POODLE(Padding Oracle On Downgraded Legacy Encryption) 취약점:**

구식 암호화 기법을 악용할 수 있게 하는 프로토콜 다운그레이드 취약점

### **► BEAST(Browser Exploit Against SSL/TLS) 취약점:**

앤드 유저 브라우저에서 HTTPS 의 쿠키들을 해독하고 효과적인 타킷의 세션을 하이제킹할 수 있는 취약점

주요 웹 브라우저 및 애플리케이션에 구현된 TLS 는 다운그레이드 협상 프로세스 (downgrade negotiation process)를 지원하기 때문에 서버가 최신 버전을 지원하더라도 공격자가 취약한 프로토콜을 이용할 수 있습니다.

****브라우져별 TLS 1.0, TLS 1.1 지원 종료 일정****

![image](https://user-images.githubusercontent.com/5876149/218312130-1f494514-782d-48a5-9703-4693cc22c9e6.png)


- Nginx 에서 TLS 1.0 , 1.1 사용하지 않는법
    - 보통 nginx의 설정 파일 ( `/etc/nginx/nginx.conf` )

```bash
ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
```

```bash
ssl_protocols TLSv1.2 TLSv1.3;
```

---

출처

- [http://www.ktword.co.kr/test/view/view.php?m_temp1=1957](http://www.ktword.co.kr/test/view/view.php?m_temp1=1957)
- [https://www.ssl.com/guide/disable-tls-1-0-and-1-1-apache-nginx/](https://www.ssl.com/guide/disable-tls-1-0-and-1-1-apache-nginx/)
- https://cert.crosscert.com/공지웹-브라우저-tls-1-0-tls-1-1-프로토콜-지원-중단-예정/
