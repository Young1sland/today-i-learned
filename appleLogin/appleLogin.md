# Apple Login 구현
https://developer.apple.com/kr/sign-in-with-apple/get-started/
Apple로 로그인을 구현하기 전에 먼저 Certificates, Identifiers & Profiles(인증서, 식별자 및 프로파일)를 사용하여 Apple Developer 계정에 식별자 및 키를 설정해야 합니다. 

App ID : 애플리케이션을 식별하는 고유 문자열. 특정 애플리케이션을 애플 개발자 서비스와 연결하는데 사용

Service ID : 웹서비스나 API를 식별하는 고유 문자열. Service ID는 웹서비스나 API에 대한 인증 및 인가 관리

## Verfify  a user

![image-20230321140255786](/Users/wisdom/Library/Application Support/typora-user-images/image-20230321140255786.png)



## Verify the identity token

인증이 되면 서버는 identity token, authorization code, user identifier를 리턴한다.
identity token
- iss : https://appleid.apple.com
- sub : 

## Generate and validate tokens
토큰을 얻기 위한 auturization code를 검증하거나 존재하는 refresh token을 검증
```
POST https://appleid.apple.com/auth/token
Request Body
    Content-Type: application/x-www-form-urlencoded
    Parts
    client_id(Required) : The identifier(App ID or Serices ID)
    client_secret(Required) : 개발자에 의해 생성된 secret JSON web Token. 개발자 계정과 연관된 private key로 가입을 할 때 사용 됨. Authoriation code와 refresh token 검증에 필요함.
    code : authorization code. 코드는 한번만 사용되며 5분간 유효하다. Authorization code 검증에 필요함.
    grant_type(Required) : authorization code 검증 시에는 'authrization_code', refresh token 검증 시에는 'refresh_token'
    refresh_token : 
    redirect_uri : 앱에 사용자 인증 시 authorization request에서 제공되는 destination URI. authorization code 검증 시 필요
```

### Validate authorization grant code





LocalStorage.shared.snsId userIdentifier = appleIDCredential.user -> id
LocalStorage.shared.snsToken identityToken = appleIDCredential.identityToken -> token


https://developer111.tistory.com/58
