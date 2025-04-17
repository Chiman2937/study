# 📝 BFC(Block Formatting Context)

<br></br>
## ✅ BFC란?
- 요소(Block, inline 등)를 배치하는 방식이자, 독립된 공간을 말함


<br></br>
## ✅ BFC의 생성 조건

> 아직 공부 안한 부분은 영어
> 
> 원본 : https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_display/Block_formatting_context

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

## ✅ 예제
### 예제링크 : https://codepen.io/fqdciufm-the-looper/pen/OPPJaoN
- 예제에서 BFC가 새로 생성된 영역은 `빨간색 테두리`가 적용 되어있다.

1. `<html>`은 `BFC`이다.
2. `BFC`가 아닌 html의 하위 요소들의 배치는 `<html>`에 의해 결정된다.
3. `inline 요소`들은 `IFC`에 의해 수평방향으로 배치되며, `IFC`에 의해 `라인블록`이 만들어진다. 이 `라인블록` 요소는 `BFC`에 의해 배치된다.
4. `div1`에 `position: absolute`를 설정하게 되면 새로운 `BFC`가 되기 때문에, `div1`의 배치는 `<html>`에 의해 결정되지 않는다.

    > `div1`은 독립된 공간이 됨
