# 모노리틱 아키텍처(Monolithic Architecture)란?

    모노리틱 아키텍처 스타일은 기존의 전통적인 웹 시스템 개발 스타일로, 하나의 애플리케이션 내에 모든 로직이 들어가 있는 구조이다.
    
<img src="./Image/MomolithicArchitecture.png
" alt="MonolithicArchitecture"></img>

    위 그림을 보면 톰캣 서버에서 도는 WAR파일 내에 사용자 관리, 상품 관리, 주문 관리 등 모든 컴포넌트가 들어 있고 이를 처리하는 UX 롲기까지 하나로 포장되서 들어가 있는 구조이다.

    이 처럼 전체 애플리케이션을 하나로 처리하기 때문에 개발 도구에서 하나의 애플리케이션만 개발하면 되고 배포 역시 간편하며 테스트도 하나의 애플리케이션만 수행하면 되므로 편리하다

출처:https://developer-jp.tistory.com/6