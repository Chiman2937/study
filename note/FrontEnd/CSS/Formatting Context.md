# 📝 Formatting Context

<br></br>
## ✅ Context란? 
- `어떤 규칙이 적용되는 독립된 범위 또는 환경`을 말한다.

## ✅ Formatting Context란?
- CSS에서 `요소의 배치 규칙이 작동하는 독립된 레이아웃 공간`을 말한다.

## ✅ Formatting Context의 종류

|종류|설명|
|---|---|
|BFC(Block Formatting Context)|블록 박스들을 배치하는 방식이자 독립된 공간|
|IFC(Inline Formatting Context)|인라인 요소들이 수평으로 배치되는 컨텍스트|

- IFC는 인라인 요소들을 모아 `라인 박스`를 만든다.
- 이 `라인 박스`들이 배치 될 때 BFC의 영향을 받는다.
  
  > 라인박스의 __"내용"__ 은 IFC의 관할
  > 
  > 라인박스의 __"위치"__ 는 BFC의 관할

<br></br>
### 🔍 비공식 용어(참고용)
- 공식적인 용어는 아니지만 실무나 교육에서 사용한다고 한다.
- 아래의 4가지는 모두 BFC에 포함되어있는 개념이다.
  
|종류|설명|
|---|---|
|FFC(Flex Formatting Context)|`display-flex`에 의해 형성되는 컨텍스트|
|GFC(Grid Formatting Context)|`display-grid`에 의해 형성되는 컨텍스트|
|TFC(Table Formatting Context)|`display-table`,`table-row`등에 의해 형성되는 컨텍스트|
|ABSFC(Absolute-positioned Formatting Context)|`position: absoulte/fixed` 요소의 독립 배치 방식|
