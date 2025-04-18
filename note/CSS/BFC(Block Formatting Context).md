# 📝 BFC(Block Formatting Context)

<br></br>
## ✅ BFC란?
- 요소(Block, inline 등)를 배치하는 방식이자, 독립된 공간을 말함


<br></br>
## ✅ BFC의 생성 조건

> 아직 공부 안한 부분은 영어
> 
> 원문 : https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_display/Block_formatting_context

|조건|
|---|
|문서의 루트 요소 (`<html>`).|
|`float: none`이 아닌 요소|
|`position: absolute/fixed`인 요소|
|`display: inline-block`인 요소|
|Table cells (elements with display: table-cell, which is the default for HTML table cells).|
|Table captions (elements with display: table-caption, which is the default for HTML table captions).|
|Anonymous table cells implicitly created by the elements with display: table, table-row, table-row-group, table-header-group, table-footer-group (which is the default for HTML tables, table rows, table bodies, table headers, and table footers, respectively), or inline-table.|
|`overflow: visible/clip` 이 아닌 요소|
|`display: flow-root`인 요소|
|<button> elements and button <input> types defaulting to display: flow-root.|
|`contain: layout, content, or paint.`인 요소|
|`display: flex or inline-flex`인 요소의 직계 자식 요소(flex items)이며 flex,grid,table container가 아닌 경우|
|`display: grid or inline-grid`인 요소의 직계 자식 요소(flex items)이며 flex,grid,table container가 아닌 경우|
|Multicol containers(테이블?) column=count 나 column-width가 auto가 아닌 위치에 있는 요소(column-count: 1인 요소를 포함)|
|column-span: all, even when the column-span: all element isn't contained by a multicol container.|

### 예제링크 : https://codepen.io/fqdciufm-the-looper/pen/OPPJaoN
- 예제에서 BFC가 새로 생성된 영역은 `빨간색 테두리`가 적용 되어있다.

1. `<html>`은 `BFC`이다.
2. `BFC`가 아닌 html의 하위 요소들의 배치는 `<html>`에 의해 결정된다.
3. `inline 요소`들은 `IFC`에 의해 수평방향으로 배치되며, `IFC`에 의해 `라인블록`이 만들어진다. 이 `라인블록` 요소는 `BFC`에 의해 배치된다.
4. `div1`에 `position: absolute`를 설정하게 되면 새로운 `BFC`가 되기 때문에, `div1`의 배치는 `<html>`에 의해 결정되지 않는다.

    > `div1`은 독립된 공간이 됨

<br></br>
## ✅ BFC의 규칙
새롭게 BFC가 정의된 요소는 다음 내용과 같은 규칙이 적용된다.

### 🔍 주요 규칙
- normal flow 기준으로 수직 정렬한다.(normal flow 규칙을 적용 받은 요소를 in flow 요소라고 함)

|구분| 명칭| 종류 |
|-|-|-|
|normal flow 규칙을 적용 받는 요소| in flow | 기본 block, inline 요소 등 |
|normal flow 규칙을 적용 받지 않는 요소 | out of flow | `float:none이 아닌` 요소,`position: absolute/fixed` 인 요소 |
  
- out of flow 요소는 normal flow의 규칙을 적용받지 않고, 해당 요소의 특성에 따라 독립적으로 배치된다.
- BFC의 자식 float 요소는 BFC에 포함되고 레이아웃에 영향을 준다.(Contain internal floats)
- BFC의 부모 float 요소는 BFC를 포함하지만 BFC는 부모 Float 요소의 레이아웃에 영향을 주지 않는다.(Exclude external floats)
- BFC의 자식 `position: absolute/fixed` 요소는 BFC에 포함은 되지만, 레이아웃에 영향을 주지 않는다.
- BFC의 부모 `position: absolute/fixed` 요소는 BFC를 포함하지만, BFC는 부모 `position: absolute/fixed` 요소의 레이아웃에 영향을 주지 않는다.
- 마진상쇄를 막는다.(supress margin collapsing)
- BFC의 자식 `overflow: visible/clip이 아닌 요소`는 부모 BFC의 레이아웃에 영향을 주지만, 이 요소의 자식은 BFC의 레이아웃에 영향을 주지 않는다.
- BFC는 부모 BFC의 레이아웃에 영향을 주지 않는다.

#### 레이아웃에 영향을 주지 않는다는 말은 아래와 같다.
> BFC의 height에 포함되지 않는다.
> 
> BFC의 요소 정렬 규칙에서 벗어난다.

### 그 외


먼저 예제를 살펴보면, `<div>` 안에 

## ✅ 예제
