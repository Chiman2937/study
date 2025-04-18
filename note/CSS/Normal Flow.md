# 📝 Normal Flow

<br></br>
## ✅ Normal Flow란?
- HTML 문서의 기본적인 레이아웃 흐름
- 특별한 속성(float, position, display 등)을 주지 않았을 때, 요소들이 자연스럽게 쌓이는 방식.
- Normal Flow에 의해 배치되는 요소를 in flow 요소라고 부른다.
  
|구분| 명칭| 종류 |
|-|-|-|
|normal flow 규칙을 적용 받는 요소| in flow | 기본 block, inline 요소 등 |
|normal flow 규칙을 적용 받지 않는 요소 | out of flow | `float:none이 아닌` 요소,`position: absolute/fixed` 인 요소 |

## ✅ Normal Flow의 주요 규칙
### 1. Block 요소는 수직 흐름으로 배치된다
- 한 줄에 하나씩 쌓임.
- 기본적으로 부모의 너비를 채움.
- 위에서 아래로 순서대로 배치됨.
- 다음 block 요소는 이전 요소의 바로 아래에 위치.

### 2. Inline 요소는 수평 흐름으로 배치된다
- 한 줄에 여러 개가 나열됨 (왼→오).
- 줄이 넘치면 줄바꿈(wrapping) 됨.
- Line box 안에서 수평 흐름을 가짐.
- 줄 간격은 line-height에 의해 조절.

### 3. 요소는 기본적으로 부모의 내용 흐름을 따름
- 자식 요소는 부모의 normal flow 안에서 배치됨.
- 따라서 부모가 block이면 자식도 수직 정렬, inline이면 수평 정렬을 따르기 쉬움.

### 4. 중첩된 마진은 상쇄된다 (margin collapsing)
- 수직 방향의 block 요소에서 인접한 마진이 겹칠 수 있음.

### 5. 요소는 z축(겹침 순서)을 고려하지 않고 평면적으로 배치된다
- z-index는 사용되지 않으며, 기본적으로 HTML 순서대로 겹침.
