# 📝 쌓임 맥락(stacking context)

<br></br>
## ✅ 쌓임 맥락이란?
- 가상의 z축을 사용하여 html요소의 배치를 3차원 관점으로 보는 것
- 요소들이 겹칠 때 어떤 것을 앞쪽으로 배치할 지 우선순위를 정하는 것

## ✅ 쌓임 맥락의 생성 조건

|조건|
|---|
|문서의 루트 요소. (<html>)|
|`position`이 `absolute` 또는 `relativ`e이고, `z-index`가 auto가 아닌 요소.|
|`position`이 `fixed` 또는 `sticky`인 요소. (sticky는 모든 모바일 브라우저에서는 해당하지만 구형 데스크톱 브라우저에서는 해당하지 않음)|
|`container queries`를 위한 container-type이 size 또는 inline-size인 요소.|
|`플렉스(flexbox)` 컨테이너의 자식 중 `z-index`가 auto가 아닌 요소.|
|`그리드(grid)` 컨테이너의 자식 중 `z-index`가 auto가 아닌 요소.|
|`opacity`가 1보다 작은 요소. (불투명도 명세 참고)|
|`mix-blend-mode`가 normal이 아닌 요소.|
|다음 속성 중 하나라도 none이 아닌 값을 가진 요소.|
|`transform`|
|`filter`|
|`backdrop-filter`|
|`perspective`|
|`clip-path`|
|`mask` / `mask-image` / `mask-border`|
|`isolation`이 isolate인 요소.|
|will-change의 값으로, 초깃값이 아닐 때 새로운 쌓임 맥락을 생성하는 속성을 지정한 요소.|
|contain이 layout, paint, 또는 둘 중 하나를 포함하는 값(strict, content 등)인 요소.|
|top layer에 배치된 요소와 이에 상응하는 ::backdrop. fullscreen 및 popover 요소들을 예로 들 수 있습니다.|

## ✅ 예제

https://codepen.io/fqdciufm-the-looper/pen/jEEOrvG

1. div2는 div1의 자식요소이다.
2. div4는 div3의 자식요소이다.
4. div1은 `position:relative`, `z-index:auto` => 쌓임맥락 생성조건에 맞지 않는다.
5. div2는 `position:absolute`, `z-index:2` => 쌓임맥락이 생성된다. 단, 부모요소인 div1은 쌓임맥락을 생성하지 않기 때문에 div2에서 가장 가까운 위치 지정 컨텍스트(positioned ancestor)은 body가 된다.
6. div3은 `position:relative`, `z-index:1` => 쌓임맥락이 생성된다.
7. div4는 `position:absolute`, `z-index:10` => 쌓임맥락이 생성된다.
