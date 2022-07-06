# Oauth2.0
> Authorization Code Grant
> * OAuth2.0 인증과정
> <img src="./Image/OAuth.png
" alt="OAuth"></img>
> * OAuth2.0 프로세스
> <img src="./Image/OAuth2.png
" alt="OAuth"></img>
> 해당 프로세스를 풀어보면 먼저 로그인 페이지로 이동하여 로그인을 진행한다. 이 부분 인증 요청 과정이다. 그리고 로그인이 완료되게 되면 인가 서버(Authorization Server)에서 인가 코드(Authorization Code)를 발급해준다. <br>
Redirect URL을 통해 우리 페이지로 이동하여 인가 코드에 대한 정보를 실제로 우리가 받을 수 있다. <br>
이 코드를 활용하여 인가 서버(Authorization Server)에 Access Token을 요청하여 발급받는다. <br>
그리고 우리는 PAYCO의 어떤 자원 혹은 서비스를 이용하고자 할 때, Access Token을 담아 리소스 서버(Resource Server)에 요청하면 서버에서 검증 후 우리가 원하는 자원 혹은 서비스를 제공해주고 우리는 이를 사용할 수 있게 되는 것이다. <br>
OAuth 2.0 방식 자체는 우리가 원하는 것처럼 이용하려는 애플리케이션에 비밀번호를 제공하지 않으면서 실제 서비스를 이용할 수 있도록 도움을 준다. 하지만 API 호출 요청에 대해 전달 받은 Access Token이 유효한 지를 확인하는 작업을 계속적으로 진행한다. 지금 같이 MSA 아키텍쳐를 구성하는 많은 서비스에서는 각각의 서버가 Access Token의 유효성 및 권한 확인을 인가 서버에 계속적으로 요청하여 병목 현상 등이 발생해 서버의 부하로 이어질 수 있는 문제가 존재한다.

이를 해결하기 위해 JWT 기반 인증 방식이 사용된다.


# JWT

JWT(Json Web Token)는 Json 포맷을 이용하여 사용자에 대한 속성을 저장하는 Claim 기반의 Web Token이다. (Claim이란 토큰에 저장된 정보들이다.)<br>
JWT는 사용자의 정보를 자체적으로 안전하게 담고 있어 토큰 자체를 정보로 사용한다.<br>
그에 따라, 한번 인증을 받은 후에는 JWT 토큰을 활용하여 자체적으로 정보가 맞는지 확인하는 방식으로 만료 기간 전까지 계속적으로 사용할 수 있는 것이다.

>JWT 구조

JWT는 Header, Payload, Signature 3 부분으로 이루어진다. <br>
각각의 부분은 Base64로 인코딩되어 표현되고 각각의 부분을 이어주기 위해 구분자를 사용하여 구분한다.
<img src="./Image/JWT.png
" alt="JWT Structure"></img>

> Header <br>

JWT를 어떻게 검증(Verify)하는가에 대한 내용을 담고 있다.<br>
토큰의 헤더에는 두 가지가 존재한다.<br>
typ - 토큰의 타입을 지정한다. (JWT)<br>
alg - 서명(Signature) 시 사용하는 알고리즘이다. (HS256, RSA 등)

---

> PayLoad <br>

토큰에 저장된 정보들인 클레임이 담겨 있다.<br>
클레임은 세 가지 종류로 나누어지며, Json 형태로 다수의 정보를 넣을 수 있다.<br>
등록된 클레임
등록된 클레임은 토큰 정보를 표현하기 위해 이미 정해진 종류의 데이터들이며 정해진 것들 중 선택적으로 골라 작성한다<br>
iss - 토큰 발급자<br>
sub - 토큰 제목<br>
aud - 토큰 대상자<br>
exp - 토큰 만료 시간<br>
nbf - 토큰 활성 날짜<br>
iat - 토큰 발급 시간<br>
jti - JWT 토큰 식별자<br>
* 공개 클레임<br>
공개 클레임은 사용자 정의 클레임으로, 공개용 정보를 위해 사용된다. <br> 충돌 방지를 위해 URI 포맷을 사용한다.
```json
{
    "https://gilssang97.tistory.com/" : true
}
```
* 비공개 클레임
  비공개 클레임은 사용자 정의 클레임으로, 서버와 클라이언트 사이에 임의로 지정한 정보를 저장한다
```json
{
    "token_type" : "access"
}
```
---
> Signature

토큰을 인코딩하거나 유효성 검증을 할 때 사용되는 고유한 암호화 코드이다.

Header와 Payload의 값을 각각 Base64로 인코딩한 후 인코딩 된 값을 Secret Key를 이용해 헤더에서 정의한 알고리즘으로 암호화하고 다시 Base64로 인코딩하여 생성한다.

# 생성된 JWT 토큰
<img src="./Image/JWTExam.png
" alt="JWT Example"></img>
```json
{
    "Authorization": "Bearer {Token}"
}
```
{Token} 자리에 생성된 토큰 값이 저장된다.
하지만 이렇게 생성되어 사용되는 JWT 토큰도 마냥 장점만 존재하는 것은 아니다.

토큰 자체에 정보를 담고 있으므로 위험 요소가 존재하고 토큰 자체에 정보를 내장하기에 크기가 커져 네트워크에 부담이 될 수 있다.

JWT는 Token으로 Stateless한 성격을 가지기에 한번 만들어지면 임의로 삭제하는 것이 불가능하므로 토큰 만료 시간을 넣어 삭제될 수 있도록 한다.

출처 : https://gilssang97.tistory.com/55