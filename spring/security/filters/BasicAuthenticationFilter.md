# BasicAuthenticationFilter

## BasicAuthenticationFilter 란 ? 
- Basic 인증에 대한 처리를 하는 필터. 보안에 취약하기에 반드시 HTTPS를 사용 권장.

## Basic 인증이란 ?
- 요청 헤더에 username와 password를 실어 보내면 브라우저 또는 서버가 그 값을 읽어서 인증하는 방식.  
`Authorization: Basic QWxhZGRpbjpPcGVuU2VzYW1l (keesun:123 을 BASE 64)`
- 한번이라도 공격자에게 스니핑 당하게 된다면 유저 정보를 노출시키게 되어 보안에 매우 취약, 반드시 HTTPS를 사용할 것을 권장한다.
- 한번 인증을 받았더라도 그 다음 인증 때 마다 요청을 보내야는 stateless한 특징을 가진다.  

