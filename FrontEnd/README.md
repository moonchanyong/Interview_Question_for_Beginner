# Part 3-1 Front-End
* [브라우저의 동작 원리](#브라우저의-동작-원리)
* Document Object Model(작성중)
  * Event bubbling and Capturing
  * Event delegation
* [CORS](#cors)
* [크로스 브라우징](#크로스-브라우징)
* [웹 성능과 관련된 Issues](#웹-성능과-관련된-issue-정리)
* [서버 사이드 렌더링 vs 클라이언트 사이드 렌더링](#서버-사이드-렌더링-vs-클라이언트-사이드-렌더링)
* [CSS Methodology](#css-methodology)
* [normalize.css vs reset.css](#normalize-vs-reset)

[뒤로](https://github.com/JaeYeopHan/for_beginner)

## 브라우저의 동작 원리
1. HTML 마크업을 처리하고 DOM 트리를 빌드한다. (**"무엇을"** 그릴지 결정한다.)
2. CSS 마크업을 처리하고 CSSOM 트리를 빌드한다. (**"어떻게"** 그릴지 결정한다.)
3. DOM 및 CSSOM을 결합하여 렌더링 트리를 형성한다. (**"화면에 그려질 것만"** 결정)
4. 렌더링 트리에서 레이아웃을 실행하여 각 노드의 기하학적 형태를 계산한다. (**"Box-Model"** 을 생성한다.)
5. 개별 노드를 화면에 페인트한다.(or 래스터화)

#### Reference
* [Naver D2 - 브라우저의 작동 원리](http://d2.naver.com/helloworld/59361)
* [Web fundamentals - Critical-rendering-path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/?hl=ko)
* [브라우저의 Critical path (한글)](http://m.post.naver.com/viewer/postView.nhn?volumeNo=8431285&memberNo=34176766)

[뒤로](https://github.com/JaeYeopHan/for_beginner)/[위로](#part-3-1-front-end)

</br>

## CORS
다른 도메인으로부터 리소스가 요청될 경우 해당 리소스는 **cross-origin HTTP 요청** 에 의해 요청된다. 하지만 대부분의 브라우저들은 보안 상의 이유로 스크립트에서의 cross-origin HTTP 요청을 제한한다. 이것을 `Same-Origin-Policy(동일 근원 정책)`이라고 한다. 요청을 보내기 위해서는 요청을 보내고자 하는 대상과 프로토콜도 같아야 하고, 포트도 같아야 함을 의미한다._(이 때, 서브 도메인 네임은 상관없다.)_

이러한 문제를 해결하기 위해 과거에는 flash를 proxy로 두고 타 도메인간 통신을 했다. 하지만 모바일 운영체제의 등장으로 flash로는 힘들어졌다. (iOS는 전혀 플래시를 지원하지 않는다.) 대체제로 나온 기술이 `JSONP(JSON-padding)`이다. jQuery v.1.2 이상부터 `jsonp`형태가 지원되 ajax를 호출할 때 타 도메인간 호출이 가능해졌다. `JSONP`에는 타 도메인간 자원을 공유할 수 있는 몇 가지 태그가 존재한다. 예를들어 `img`, `iframe`, `anchor`, `script`, `link` 등이 존재한다.

여기서 `CORS`는 타 도메인 간에 자원을 공유할 수 있게 해주는 것이다. `Cross-Origin Resource Sharing` 표준은 웹 브라우저가 사용하는 정보를 읽을 수 있도록 허가된 **출처 집합** 를 서버에게 알려주도록 허용하는 특정 HTTP 헤더를 추가함으로써 동작한다.

|HTTP Header|Description|
|:-:|:-:|
|Access-Control-Allow-Origin|접근 가능한 `url` 설정|
|Access-Control-Allow-Credentials|접근 가능한 `쿠키` 설정|
|Access-Control-Allow-Headers|접근 가능한 `헤더` 설정|
|Access-Control-Allow-Methods|접근 가능한 `http method` 설정|

### Preflight Request
실제 요청을 보내도 안전한지 판단하기 위해 preflight 요청을 먼저 보내는 방법을 말한다. 즉, `Preflight Request`는 실제 요청 전에 인증 헤더를 전송하여 서버의 허용 여부를 미리 체크하는 테스트 요청이다. 이 요청으로 트래픽이 증가할 수 있는데 서버의 헤더 설정으로 캐쉬가 가능하다. 서버 측에서는 브라우저가 해당 도메인에서 CORS 를 허용하는지 알아보기 위해 preflight 요청을 보내는데 이에 대한 처리가 필요하다. preflight 요청은 HTTP의 `OPTIONS` 메서드를 사용하며 `Access-Control-Request-*` 형태의 헤더로 전송한다.

이는 브라우저가 강제하며 HTTP `OPTION` 요청 메서드를 이용해 서버로부터 지원 중인 메서드들을 내려 받은 뒤, 서버에서 `approval(승인)` 시에 실제 HTTP 요청 메서드를 이용해 실제 요청을 전송하는 것이다.

#### Reference
* [MDN - HTTP 접근 제어 CORS](https://developer.mozilla.org/ko/docs/Web/HTTP/Access_control_CORS)
* [Cross-Origin-Resource-Sharing에 대해서](http://homoefficio.github.io/2015/07/21/Cross-Origin-Resource-Sharing/)
* [구루비 - CORS에 대해서](http://wiki.gurubee.net/display/SWDEV/CORS+%28Cross-Origin+Resource+Sharing%29)

[뒤로](https://github.com/JaeYeopHan/for_beginner)/[위로](#part-3-1-front-end)

</br>

## 크로스 브라우징
웹 표준에 따라 개발을 하여 서로 다른 OS 또는 플랫폼에 대응하는 것을 말한다. 즉, 브라우저의 렌더링 엔진이 다른 경우에 인터넷이 이상없이 구현되도록 하는 기술이다. 웹 사이트를 서로 비슷하게 만들어 어떤 **환경** 에서도 이상없이 작동되게 하는데 그 목적이 있다. 즉, 어느 한쪽에 최적화되어 치우치지 않도록 공통요소를 사용하여 웹 페이지를 제작하는 방법을 말한다.

### 참고자료
* [크로스 브라우징 이슈에 대응하는 프론트엔드 개발자들의 전략](http://asfirstalways.tistory.com/237)

[뒤로](https://github.com/JaeYeopHan/for_beginner)/[위로](#part-3-1-front-end)

</br>

## 웹 성능과 관련된 Issue 정리
[ppt자료](https://www.slideshare.net/mixed/html5-50)
### 1. 네트워크 요청에 빠르게 응답하자
- `3.xx` 리다이렉트를 피할 것
  + 리다이렉트는 페이지를 더 느리게 만든다.
  + 리다읽트가 완료되고 HTML문서가 다운로드 되기 전까지 아무것도 렌더링 되지 않는다.
  + 리다이렉트는 HTML문서 자체의 다운로드를 지연 시키기 때문에 안좋다.
- `meta-refresh` 사용금지
  + 똑같이 리다이렉션을 해주는 역할
  + 위와 같은 이유로 성능이 다운된다.
- `CDN(content delivery network)`을 사용할 것
  + 노드가 여러지역에 나누어져 있어서 더 빠른 응답을 기대할 수 있다.
  + cdn서버에서 캐싱을 해둔다.
- 동시 커넥션 수를 최소화 할 것
  + 같은 도메인이라도 유저를 나누어 받으면 빨라진다.
- 커넥션을 재활용할 것
  + HTTP1.1에 keep-Alive기능이 있다. 

### 2. 자원을 최소한의 크기로 내려받자
- 777K
  + 이것의 정체는 무엇이냐.. 사람들이 파일을 777k로올린건 봣는데..
- `gzip` 압축을 사용할 것
  + reauest: Accept-Encoding: gzip, Content-Encoding: gzip
  + 대역폭용량을 낮춰준다.
- `HTML5 App cache`를 활용할 것
  + 이미 요청한 자원을 다시 요청 할 필요없이 캐시에서 가져온다.
  + 세가지 이점
    * 오프라인 브라우징
    * 속도
    * 서버 부하 감소
  + 사용 방법은 애플리케이션 페이지의 html엘리먼트에 manifest(캐싱 할 자원 리스트)추가
- 자원을 캐시 가능하게 할 것
- 조건 요청을 보낼 것
  + 웹 캐시를 통해 사용자가 느끼는 응답시간을 줄일 수 있지만 웹캐시 서버에ㅔ 있는 데이터가 가장 최근의 것인지 확인하는 작업이 필요하다
  + 데이터 무결성 측면과 무조건 앱캐시 할 수없는경우에 사용하면 좋다.
  + 수정 된 경우에만 리소스를 다운받으니까.

### 3. 효율적인 마크업 구조를 구축하자
- 레거시 IE 모드는 http 헤더를 사용할 것
- @import 의 사용을 피할 것
- 페이지의 위에 css를 넣을 것
  + 점진적인 렌더링
    * 하단에 위치한경우 스타일시트를 다 받을 때까지 기다리는 동안 페이지 렌더링을 지연시킨다. 하단에 넣는다면 브라우저가 점진적인 렌더링을 못하게 하는 것이다.
    * HEAD에서 LINK태그를 이용해서 첨부해야한다.
- inline 스타일과 embedded 스타일은 피할 것
- 사용하는 스타일만 CSS에 포함할 것
- 중복되는 코드를 최소화 할 것
  + 캐시에 저장 안되고 리로드 할경우 추가적인 HTTP를 보낸다.
  + 시스템에 자바스크립트 관리 모듈을 만들어 사용하라고 한다.
- 단일 프레임워크를 사용할 것
- Third Party 스크립트를 삽입하지 말 것
- 스크립트는 아래에 넣는다.
  + 스크립트의 경우 스크립트 아래에 있는 모든 구성요소의 점진적 렌더링을 막는다.
  + 스크립트가 다운로드 되는동안 동시 다운로드가 되지 않는다.
### 4. 미디어 사용을 개선하자
- 이미지 스프라이트를 사용할 것
  + 이미지를 연결해 붙여놓은것
  + 이미지를 한번만 다운받으므로 효율적이다, css background-position을이용하여 몇번째사용할지 정한다.
- 실제 이미지 해상도를 사용할 것
- CSS3를 활용할 것
- 하나의 작은 크기의 이미지는 DataURL을 사용할 것
- 비디오의 미리보기 이미지를 만들 것

### 5. 빠른 자바스크립트 코드를 작성하자
- 코드를 최소화할 것
- 필요할 때만 스크립트를 가져올 것 : flag 사용
   + flag가 off이고 코드가 필요할 때 scripttag를 돔에 추가하여 가져온다.
- DOM에 대한 접근을 최소화 할 것 : Dom manipulate는 느리다.
   + 빈도를 줄이는 것 뿐아니라 불러오는 dom을 최소화
- 다수의 엘리먼트를 찾을 때는 selector api를 사용할 것.
- 마크업의 변경은 한번에 할 것 : temp 변수를 활용
   + 미리 구성해서 넣는것과 넣어서 구성하는것 다르다.
- DOM의 크기를 작게 유지할 것.
- 내장 JSON 메서드를 사용할 것.

### 6. 애플리케이션의 작동원리를 알고 있자.
- Timer 사용에 유의할 것.
- `requestAnimationFrame` 을 사용할 것
- 활성화될 때를 알고 있을 것

#### Reference
* [HTML5 앱과 웹사이트를 보다 빠르게 하는 50가지 - yongwoo Jeon](https://www.slideshare.net/mixed/html5-50)

[뒤로](https://github.com/JaeYeopHan/for_beginner)/[위로](#part-3-1-front-end)

</br>

## 서버 사이드 렌더링 vs 클라이언트 사이드 렌더링
* 그림과 함께 설명하기 위해 일단 블로그 링크를 추가한다.
* http://asfirstalways.tistory.com/244

[뒤로](https://github.com/JaeYeopHan/for_beginner)/[위로](#part-3-1-front-end)

</br>

## CSS Methodology
`SMACSS`, `OOCSS`, `BEM`에 대해서 소개한다.

### SMACSS(Scalable and Modular Architecture for CSS)
`SMACSS`의 핵심은 범주화이며(`categorization`) 스타일을 다섯 가지 유형으로 분류하고, 각 유형에 맞는 선택자(selector)와 작명법(naming convention)을 제시한다.
* 기초(Base)
  * element 스타일의 default 값을 지정해주는 것이다. 선택자로는 요소 선택자를 사용한다.
* 레이아웃(Layout)
  * 구성하고자 하는 페이지를 컴포넌트를 나누고 어떻게 위치해야하는지를 결정한다. `id`는 CSS에서 클래스와 성능 차이가 없는데, CSS에서 사용하게 되면 재사용성이 떨어지기 때문에 클래스를 주로 사용한다.
* 모듈(Module)
  * 레이아웃 요소 안에 들어가는 더 작은 부분들에 대한 스타일을 정의한다. 클래스 선택자를 사용하며 요소 선택자는 가급적 피한다. 클래스 이름은 적용되는 스타일의 내용을 담는다.
* 상태(States)
  * 다른 스타일에 덧붙이거나 덮어씌워서 상태를 나타낸다. 그렇기 때문에 자바스크립트에 의존하는 스타일이 된다. `is-` prefix를 붙여 상태를 제어하는 스타일임을 나타낸다. 특정 모듈에 한정된 상태는 모듈 이름도 이름에 포함시킨다.
* 테마(Theme)
  * 테마는 프로젝트에서 잘 사용되지 않는 카테고리이다. 사용자의 설정에 따라서 css를 변경할 수 있는 css를 설정할 때 사용하게 되며 접두어로는 `theme-`를 붙여 표시한다.

</br>

### OOCSS(Object Oriented CSS)
객체지향 CSS 방법론으로 2 가지 기본원칙을 갖고 있다.

* 원칙 1. 구조와 모양을 분리한다.
  * 반복적인 시각적 기능을 별도의 스킨으로 정의하여 다양한 객체와 혼합해 중복코드를 없앤다.
* 원칙 2. 컨테이너와 컨텐츠를 분리한다.
  * 스타일을 정의할 때 위치에 의존적인 스타일을 사용하지 않는다. 사물의 모양은 어디에 위치하든지 동일하게 보여야 한다.

</br>

### BEM(Block Element Modifier)
웹 페이지를 각각의 컴포넌트의 조합으로 바라보고 접근한 방법론이자 규칙(Rule)이다. SMACSS가 가이드라인이라는 것에 비해서 좀 더 범위가 좁은 반면 강제성 측면에서 다소 강하다고 볼 수 있다. BEM은 CSS로 스타일을 입힐 때 id를 사용하는 것을 막는다. 또한 요소 셀렉터를 통해서 직접 스타일을 적용하는 것도 불허한다. 하나를 더 불허하는데 그것은 바로 자손 선택자 사용이다. 이러한 규칙들은 재사용성을 높이기 위함이다.

* Naming Convention
  * 소문자와 숫자만을 이용해 작명하고 여러 단어의 조합은 하이픈(`-`)과 언더바(`_`)를 사용하여 연결한다.
* BEM의 B는 “Block”이다.
  * 블록(block)이란 재사용 할 수 있는 독립적인 페이지 구성 요소를 말하며, HTML에서 블록은 class로 표시된다. 블록은 주변 환경에 영향을 받지 않아야 하며, 여백이나 위치를 설정하면 안된다.
* BEM의 E는 “Element”이다.
  * 블록 안에서 특정 기능을 담당하는 부분으로 block_element 형태로 사용한다. 요소는 중첩해서 작성될 수 있다.
* BEM의 M는 “Modifier”이다.
  * 블록이나 요소의 모양, 상태를 정의한다. `block_element-modifier`, `block—modifier` 형태로 사용한다. 수식어에는 불리언 타입과 키-값 타입이 있다.

</br>

#### Reference
* [CSS 방법론에 대해서](http://wit.nts-corp.com/2015/04/16/3538)
* [CSS 방법론 SMACSS에 대해 알아보자](https://brunch.co.kr/@larklark/1)
* [BEM에 대해서](https://en.bem.info/)

[뒤로](https://github.com/JaeYeopHan/for_beginner)/[위로](#part-3-1-front-end)

</br>

## normalize vs reset
브라우저마다 기본적으로 제공하는 element의 style을 통일시키기 위해 사용하는 두 `css`에 대해 알아본다.

### reset.css
`reset.css`는 기본적으로 제공되는 브라우저 스타일 전부를 **제거** 하기 위해 사용된다. `reset.css`가 적용되면 `<H1>~<H6>`, `<p>`, `<strong>`, `<em>` 등 과 같은 표준 요소는 완전히 똑같이 보이며 브라우저가 제공하는 기본적인 styling이 전혀 없다.

### normalize.css
`normalize.css`는 브라우저 간 일관된 스타일링을 목표로 한다. `<H1>~<H6>`과 같은 요소는 브라우저간에 일관된 방식으로 굵게 표시됩니다. 추가적인 디자인에 필요한 style만 CSS로 작성해주면 된다.

즉, `normalize.css`는 모든 것을 "해제"하기보다는 유용한 기본값을 보존하는 것이다. 예를 들어, sup 또는 sub와 같은 요소는 `normalize.css`가 적용된 후 바로 기대하는 스타일을 보여준다. 반면 `reset.css`를 포함하면 시각적으로 일반 텍스트와 구별 할 수 없다. 또한 normalize.css는 reset.css보다 넓은 범위를 가지고 있으며 HTML5 요소의 표시 설정, 양식 요소의 글꼴 상속 부족, pre-font 크기 렌더링 수정, IE9의 SVG 오버플로 및 iOS의 버튼 스타일링 버그 등에 대한 이슈를 해결해준다.

[뒤로](https://github.com/JaeYeopHan/for_beginner)/[위로](#part-3-1-front-end)

</br>

</br>

_Front-End.end_  
