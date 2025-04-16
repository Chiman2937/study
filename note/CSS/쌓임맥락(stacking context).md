# 📝 쌓임 맥락(stacking context)

<br></br>
## ✅ 쌓임 맥락이란?
- 가상의 z축을 사용하여 html요소의 배치를 3차원 관점으로 보는 것
- 요소들이 겹칠 때 어떤 것을 앞쪽으로 배치할 지 우선순위를 정하는 것

### ✔️position
- 문서 상에 요소를 배치하는 방법을 결정하는 속성
### ✔️z-index
- 위치 지정 요소(position)의 배치 순서를 결정하는 속성

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
|다음 속성 중 하나라도 none이 아닌 값을 가진 요소.<br></br>`transform`,`filter`,`backdrop-filter`,`perspective`,`clip-path`|
|`mask` / `mask-image` / `mask-border`|
|`isolation`이 isolate인 요소.|
|will-change의 값으로, 초깃값이 아닐 때 새로운 쌓임 맥락을 생성하는 속성을 지정한 요소.|
|contain이 layout, paint, 또는 둘 중 하나를 포함하는 값(strict, content 등)인 요소.|
|top layer에 배치된 요소와 이에 상응하는 ::backdrop. fullscreen 및 popover 요소들을 예로 들 수 있습니다.|

## ✅ 예제
### 예제링크 : https://codepen.io/fqdciufm-the-looper/pen/jEEOrvG
![image](https://github.com/user-attachments/assets/ef481450-aaa2-484d-89ec-bc47b983a87d)



1. `div2`는 `div1`의 자식요소이다.
2. `div4`는 `div3`의 자식요소이다.
4. `div1`은 `position:relative`, `z-index:auto` => 쌓임맥락 생성조건에 맞지 않는다.
5. `div2`는 `position:absolute`, `z-index:2` => 쌓임맥락이 생성된다.

    > 단, 부모요소인 `div1`은 쌓임맥락을 생성하지 않기 때문에 `div2`에서 가장 가까운 위치 지정 컨텍스트(positioned ancestor)은 `루트`가 된다.
    > 
    > 따라서 `div2`의 쌓임맥락은 `div1`의 영향을 받지 않는다.

6. `div3`은 `position:relative`, `z-index:1` => 쌓임맥락이 생성된다.
7. `div4`는 `position:absolute`, `z-index:10` => 쌓임맥락이 생성된다.

    > `div4`의 가장 가까운 위치 지정 컨텍스트는 `div3`이므로, `div4`는 `div3` 위에 배치된다.

### 결론 : `div1` → `div3` → `div4` → `div2`
- z-index가 가장 높은 `div2`가 제일 위에 배치
- z-index가 두번째로 높은 `div3`과 그 위에 자식요소인 `div4` 배치
- 쌓임맥락을 생성하지 않는 `div1`이 맨 아래에 배치
