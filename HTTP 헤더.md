HTTP 헤더는 클라이언트와 서버가 요청 또는 응답으로 부가적인 정보를 전송할 수 있도록 해줍니다. HTTP 헤더는 대소문자를 구분하지 않는 이름과 콜론 '`:`
' 다음에 오는 값(줄 바꿈 없이)으로 이루어져있습니다. 값 앞에 붙은 빈 문자열은 무시됩니다.

커스텀 등록 헤더는 'X-'를 앞에 붙여 추가될 수 있지만, 이 관례는 [RFC 6648](https://tools.ietf.org/html/rfc6648)
에서 비표준 필드가 표준이 되었을때 불편함을 유발하는 이유로 2012년 6월에 폐기되었습니다.

내가 보고싶은거 위주로 정리

## **공통 헤더**

요청과 응답에 모두 사용되는 헤더입니다. 이 중에서 Content 시리즈는 엔티티 헤더라고 불립니다.

### **Connection**

> Connection: keep-alive
> 

HTTP/2를 사용하지 않는다면 보통 HTTP/1.1을 사용하게 되는데요. Connection은 기본적으로 keep-alive로 되어있는데 사실상 아무런 의미도 없습니다. HTTP/2에서는 아예 사라져버렸습니다.

### **Cache-Control**

매우 중요하고 알아두어야 하는 헤더이기 때문에 따로 강좌를 빼서 알려드립니다.

많은 옵션들이 있지만, 자주 쓰이는 옵션만 알아봅니다.

먼저 아무것도 캐싱하지 않으려면

> Cache-Control: no-store
> 

를 하면됩니다. 또는 no-cache, no-store, must-revalidate로 no 시리즈를 다 붙여줍니다.

> Cache-Control: no-cache
> 

는 가장 많이 헷갈려하는 헤더 설정인데요. no-cache이지만 cache하지 말라는 뜻이 아닙니다!!! 모든 캐시를 쓰기 전에 서버에 이 캐시 진짜 써도 되냐고 물어보라는 뜻입니다.

> Cache-Control: must-revalidate
> 

must-revalidate는 만료된 캐시만 서버에 확인을 받도록 하는 겁니다. no-cache랑 must-revalidate는 이름이 잘못 지어진 감이 있습니다.

> Cache-Control: public 또는 private
> 

public이면 공유 캐시(또는 중개 서버)에 저장해도 된다는 뜻이고 private이면 브라우저같은 특정 사용자 환경에만 저장하라는 뜻입니다.

> Cache-Control: public, max-age=3600
> 

max-age로 캐시 유효시간을 줄 수 있습니다. 초 단위이므로 위 예제에서는 1시간입니다. 1시간이 지나면 이 응답 캐시는 만료된 것으로 여겨집니다.

참고로 위의 옵션들은 혼합해서 써도 됩니다. no-store, no-cache, must-revalidate처럼 콤마로만 구분하면 되고요.

Cache-Control을 응답 헤더라고 생각하실 수도 있는데, 요청 헤더로도 사용할 수 있습니다. 프론트 - 중개 서버 - 진짜 서버와 같은 구조인 경우에 중개 서버에 있는 캐시를 가져오지 않도록 하려면 요청 시부터 Cache-Control을 헤더로 넣어주곤 합니다.

### **Content-Type**

> Content-Type: text/html; charset=utf-8
> 

컨텐츠의 타입(MIME)과 문자열 인코딩(utf-8 등등)을 명시할 수 있습니다. 조금 뒤에 나오는 Accept 헤더, Accept-Charset 헤더와 대응됩니다. 위에 예시로 든 헤더는 현재 메시지 내용이 text/html 타입이고 문자열은 utf-8 문자열임을 알려줍니다.

프런트엔드에서 서버로 데이터를 보낼 때는 text/html 이런 것 대신 www-url-form-encoded나 multipart/form-data같은 게 Content-Type이 됩니다.

## **요청 헤더**

### **Host**

서버의 도메인 네임이 나타나는 부분입니다(포트 포함).

> Host: www.zerocho.com
> 

Host 헤더는 반드시 하나가 존재해야 합니다.

### **User-Agent**

Host보다 더 유명한 헤더는 User-Agent입니다. 현재 사용자가 어떤 클라이언트(운영체제와 브라우저 같은 것)를 이용해 요청을 보냈는지 나옵니다.

> User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36
> 

제 클라이언트가 뭔지 맞춰보세요 ㅎㅎ. 저는 맥북 크롬으로 접속했습니다. 물론 유저 에이전트를 믿어서는 안됩니다. 헤더는 변경할 수 있으니까요. 하지만 대부분의 사람들이 유저 에이전트를 조작하지 않고 그대로 보내기 때문에, 유저 에이전트 헤더를 활용해서 접속자 통계 등을 내곤 합니다. 또한 이를 활용해서 IE로 접속한 사람들을 찾아낸 후, IE는 지원하지 않으니 크롬으로 접속해주세요와 같은 메시지를 표시하기도 하고요.

### **Accept**

Accept 시리즈를 알아봅시다. Accept 헤더는 요청을 보낼 때 서버에 이런 타입(MIME)의 데이터를 보내줬으면 좋겠다고 명시할 때 사용합니다. 예를 들어 요청의 헤더로

> Accept: text/html
> 

를 보내면 HTML 형식인 응답을 처리하겠다는 뜻입니다.

> Accept: image/png, image/gif
> 

콤마로 여러 타입을 동시에 적어줄 수도 있고, *(와일드카드)로 "텍스트이기만 하면 돼"라고 적어줄 수도 있습니다.

Accept 시리즈라고 한 이유는 Accept-Encoding, Accept-Charset, Accept-Language 등도 있기 때문인데요. 공통 헤더의 Content 시리즈와 대응됩니다. Accept로 원하는 형식을 보내면, 서버가 그에 맞춰 보내주면서 응답 헤더의 Content를 알맞게 설정하겠죠.

> Accept-Charset: utf-8
> 

Charset은 문자 인코딩(UTF-8 등)을 명시하는 부분이고, Language는 원하는 언어, Encoding은 원하는 컨텐츠 압축 방식입니다.

뭘 적어야할지 모르겠다면 *(와일드카드)를 적거나, 그냥 브라우저가 알아서 설정해서 보내는 Accept를 사용하면 됩니다.

### **Authorization**

Authorization 헤더는 인증 토큰(JWT든, Bearer 토큰이든)을 서버로 보낼 때 사용하는 헤더입니다. API 요청같은 것을 할 때 토큰이 없으면 거절당하기 때문에 이 때, Authorization을 사용하면 됩니다.

> Authorization: Bearer XXXXXXXXXXXXX
> 

보통 Basic이나 Bearer같은 토큰의 종류를 먼저 알리고 그 다음에 실제 토큰 문자를 적어 보냅니다.

### **Origin**

POST같은 요청을 보낼 때, 요청이 어느 주소에서 시작되었는지를 나타냅니다. 여기서 요청을 보낸 주소와 받는 주소가 다르면 **[CORS 문제가](https://www.zerocho.com/category/NodeJS/post/5a6c347382ee09001b91fb6a)** 발생하기도 합니다.

## **응답 헤더**

### **Access-Control-Allow-Origin**

프론트엔드 개발자들에게 악명 높은 헤더입니다. 요청을 보내는 프론트 주소와 받는 백엔드 주소가 다르면 **[CORS 에러](https://www.zerocho.com/category/NodeJS/post/5a6c347382ee09001b91fb6a)**가 발생하는데요. 이 때 서버에서 응답 메시지 Access-Control-Allow-Origin 헤더에 프론트 주소를 적어주어야 에러가 나지 않습니다.

> Access-Control-Allow-Origin: www.zerocho.com
> 

프로토콜, 서브도메인, 도메인, 포트 중 하나만 달라도 CORS 에러가 나서 슬픕니다. 만약 주소를 일일이 지정하기 싫다면 *으로 모든 주소에 CORS 요청을 허용하면 됩니다. 단 그만큼 보안이 취약해집니다.

유사한 헤더로 Access-Control-Request-Method, Access-Control-Request-Headers, Access-Control-Allow-Methods, Access-Control-Allow-Headers 등이 있습니다. Request랑 Allow에서 Method 단수 복수 주의하세요!

CORS 요청 시에는 미리 **OPTIONS 주소**로 서버가 CORS를 허용하는지 물어봅니다. 이 때 Access-Control-Request-Method로 실제로 보내고자 하는 메서드를 알리고, Access-Control-Request-Headers로 실제로 보내고자 하는 헤더들을 알립니다. Allow 친구들은 Request에 대응되는 애들로, 서버가 허용하는 메서드와 헤더를 응답하는데 사용됩니다. Request랑 Allow가 일치하면 CORS 요청이 이루어지는 것이죠.

### **Allow**

Allow 헤더는 Access-Control-Allow-Methods랑 비슷하지만, CORS 요청 외에도 적용된다는 데에 차이가 있습니다. 즉 GET www.zerocho.com은 되고, POST www.zerocho.com은 허용하지 않는 경우, 405 Method Not Allowed 에러를 응답하면서 헤더로

> Allow: GET
> 

를 같이 보내면 됩니다. GET 요청만 받겠다는 뜻입니다.

### **Location**

300번대 응답이나 201 Created 응답일 때 어느 페이지로 이동할지를 알려주는 헤더입니다.

> HTTP/1.1 302 Found
> 

이런 응답이 왔다면 브라우저는 / 주소로 리다이렉트합니다.

### **Content-Security-Policy**

다른 외부 파일들을 불러오는 경우, 차단할 소스와 불러올 소스를 여기에 명시할 수 있습니다. 하나의 웹 페이지는 다양한 외부 소스들을 불러옵니다. 이미지도 불러오고 script 태그로 자바스크립트 파일들도 불러옵니다. 폰트나 스타일, 아이프레임도 불러오고요. 하지만 해커들에 의해 원하지 않는 파일을 불러오게 될 수도 있습니다. 악성 코드가 담겨져있는 파일이라면 큰 일이 나겠죠. XSS 공격 같은 것이 하나의 예시입니다. 이럴 때 Content-Security-Policy로 허용할 외부 소스만 지정할 수 있습니다.

> Content-Security-Policy: default-src 'self'
> 

self로 지정하면 자신의 도메인의 파일들만 가져올 수 있습니다. www.zerocho.com에서는 www.zerocho.com/logo.jpg를 가져올 수 있지만, www.nero.com/logo.jpg는 못 가져오는 것이죠. https:로 지정하면 https를 통해서만 파일을 가져올 수 있게 됩니다. 'none'으로 지정하면 가져올 수 없습니다.

default-src는 모든 외부 소스에 대해 적용되는 것이고요. 각각 따로 지정할 수도 있습니다. 두 개나 세 개 정도만 추려서 지정할 수도 있고요.

> Content-Security-Policy: font-src 'self'; script-src https://www.zerocho.com 'unsafe-inline'; img-src 'self'; style-src 'self' 'unsafe-inline'; object-src 'none'
> 

font-src, script-src, img-src, style-src, object-src 등이 있고, 소스 옵션으로는 도메인이나, https:, 'unsafe-inline'(인라인 태그 허용), 'unsafe-eval'(eval 함수 허용) 등이 있습니다. 옵션들이 매우 많으므로 자세한 내용은 **[여기](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)** 서 참고하세요!

## **쿠키**

쿠키는 브라우저에 저장되는 작은 데이터 조각으로, 임시 데이터 보관 또는 웹페이지 개인화 등에 사용됩니다. 쿠키를 주기적으로 지우지 않으면 브라우저에 엄청나게 많은 쿠키들이 쌓여 있는 것을 보게 되실텐데 이것들이 여러분을 추적하고 있는 것입니다.

### **Set-Cookie**

서버에서 클라이언트(브라우저)한테 이런 이런 쿠키를 저장하라고 명령하는 응답 헤더입니다.

> Set-Cookie: 키=값; 옵션들
> 

Set-Cookie: hello=zero면 hello라는 키에 값을 zero로 해서 보낼 수 있는거죠. 옵션들도 몇 개 알려드리겠습니다.

- Expires: 쿠키 만료 날짜를 알려줄 수 있습니다.
- Max-Age: 쿠키 수명을 알려줄 수 있습니다. Expires는 무시됩니다.
- Secure: https에서만 쿠키가 전송됩니다.
- HttpOnly: 자바스크립트에서 쿠키에 접근할 수 없습니다. XSS 요청을 막으려면 활성화해두는 것이 좋습니다.
- Domain: 도메인을 적어주면 도메인이 일치하는 요청에서만 쿠키가 전송됩니다. 가끔 도메인이 다른 쿠키들이 있는데, 이런 쿠키들은 써드 파티 쿠키로 여러분을 추적하고 있는 쿠키입니다. 구글이나 페이스북같은 곳이 써드 파티 쿠키를 적극적으로 사용합니다.
- Path: 패스를 적어주면 이 패스와 일치하는 요청 요청에서만 쿠키가 전송됩니다.

예를 들면 다음과 같이 가능합니다.

> Set-Cookie: zerocho=babo; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
> 

쿠키는 XSS 공격과 CSRF 공격 등에 취약하기 때문에 HttpOnly 옵션을 켜두고, 쿠키를 사용하는 요청은 서버 단에서 검증하는 로직을 꼭 마련해두는 것이 좋습니다.

---

출처

- [https://developer.mozilla.org/ko/docs/Web/HTTP/Headers](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers)
- [https://www.zerocho.com/](https://www.zerocho.com/category/HTTP/post/5b3ba2d0b3dabd001b53b9db)
