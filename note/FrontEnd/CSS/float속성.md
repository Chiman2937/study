# 📝 CSS float 속성

<br></br>
## ✅ float
- float속성은 요소를 어떻게 배치할지 지정하는 속성이다.

## ✅ float의 특징
- float속성이 적용된 요소는 원래의 흐름에서 벗어난다.(원래 자신이 차지하고 있던 공간을 잃는다.)
- float속성이 적용된 요소의 원래 자리는 그 다음 요소가 차지하는데, float속성이 적용된 요소의 아래쪽으로 배치된다(마치 쌓임맥락이 형성되는 것처럼 보인다.)
  
  > 예제1 : `div2`에 `float: left`를 적용한 경우(https://codepen.io/fqdciufm-the-looper/pen/zxxYvEL)
  
<br></br>
- float속성이 적용된 요소의 원래 자리를 다른 요소가 차지할 때, 텍스트는 그 공간을 차지하지 못하고 밀려난다.
- 재밌는 점은 기본적으로 텍스트는 옆으로 밀려나는데, 밀려난 텍스트 중 문자열이 남은 요소 공간보다 더 길면 float속성이 적용된 요소의 아래쪽으로 밀려난다.

  > 예제2 : `div3`에 긴 문자열을 추가할 경우(https://codepen.io/fqdciufm-the-looper/pen/QwwWyKr)

- float속성을 사용하면 쌓임맥락이 생성된다고 착각할 수 있는데 그냥 단어 의미 그대로 앞쪽으로 튀어나온다고 생각하면 된다.
- position이 적용되어 쌓임맥락이 생성된 요소보다 무조건 뒤쪽에 배치된다.(`position: static` < `float` < `position: relative 등`)
<br></br>
- 부모가 BFC가 아닐 경우 부모요소는 float요소를 감싸지 않고 가장 가까운 조상 BFC가 float요소를 감싼다.

  > 예제 : https://codepen.io/fqdciufm-the-looper/pen/vEEEayQ

  > 예제에서 `<div2>`는 BFC가 아닌`<div1>`의 레이아웃에 영향을 주지 않지만, 상위 BFC인 `<html>`의 레이아웃에 영향을 주고 있다.
  >
  > 즉, float요소는 부모 요소가 BFC가 아니라면 위치는 부모를 기준으로 배치되지만 레이아웃에 영향을 주지 않고, 조상 중 BFC가 있다면 해당 BFC의 레이아웃에 영향을 준다(예제에서는 `<html>`)
  >
  > `float: left` 버튼을 눌러 `<div1>`에 float 속성을 적용시켜보면,`<div1>`은 이제 BFC가 되었기 때문에 `<div2>`가 `<div1>`의 레이아웃에 영향을 주게 된다.
  
  |기준|기준이 되는 조상|
  |---|---|
  |위치|부모 요소를 기준으로 배치됨|
  |레이아웃|가장 가까운 BFC인 조상의 레이아웃에 영향을 줌|

<br></br>
