# 📝 CSS float 속성

<br></br>
## ✅ float
- float속성은 요소를 어떻게 배치할지 지정하는 속성이다.

## ✅ float의 특징
- float속성이 적용된 요소는 원래의 흐름에서 벗어난다.(원래 자신이 차지하고 있던 공간을 잃는다.)
- float속성이 적용된 요소의 원래 자리는 그 다음 요소가 차지하는데, float속성이 적용된 요소의 아래쪽으로 배치된다(마치 쌓임맥락이 형성되는 것처럼 보인다.)
> 예제1 : `div2`에 `float: left`를 적용한 경우(https://codepen.io/fqdciufm-the-looper/pen/zxxYvEL)

- float속성이 적용된 요소의 원래 자리를 다른 요소가 차지할 때, 텍스트는 그 공간을 차지하지 못하고 밀려난다.
- 재밌는 점은 기본적으로 텍스트는 옆으로 밀려나는데, 밀려난 텍스트 중 문자열이 남은 요소 공간보다 더 길면 float속성이 적용된 요소의 아래쪽으로 밀려난다.

> 예제2 : `div3`에 긴 문자열을 추가할 경우(https://codepen.io/fqdciufm-the-looper/pen/QwwWyKr)

- float속성을 사용하면 쌓임맥락이 생성된다고 착각할 수 있는데 그냥 단어 의미 그대로 앞쪽으로 튀어나온다고 생각하면 된다.
- position이 적용되어 쌓임맥락이 생성된 요소보다 무조건 뒤쪽에 배치된다.(`position: static` < `float` < `position: relative 등`)

<br></br>
