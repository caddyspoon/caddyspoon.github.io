# [Javascript] 사용자의 입력 완료까지 기다리기 "debouncing"


> 사용자의 입력을 기다렸다가 완전히 입력이 종료되었다고 판단되면 입력을 받도록 합니다. 디바운싱을 활용해서요.

## 디바운싱이란?
사용자의 입력을 받아 처리해야 합니다. 단, `사용자의 모든 입력이 종료되었을 때`만 입력을 받고 `엔터` 키를 누르는 등 입력이 종료되었다는 별도의 액션이 없습니다. 사용자의 입력이 완료 되었음을 어떻게 알고 처리할 수 있을까요? 

간단해보이지만 실제로 구현하고자 하면 생각보다 쉽지 않을 것입니다. 이러한 문제를 해결하기 위한 개념이 `deboucing` 입니다.

{{< admonition info "디바운싱(debouncing)" >}}

기계식 스위치의 동작을 전기적 신호로 바꿀 때 생기는 진동 잡음을 제거하기 위하여 사용하는 하드웨어의 지연 회로, 소프트웨어의 적절한 지연 시간.

출처 : 컴퓨터 인터넷 IT용어 대사전 | 네이버 사전

{{< /admonition >}}

사전에서는 목적에 맞는 처리를 위한 소프트웨어의 적절한 지연 시간을 디바운싱이라고 정의하고 있습니다. 디바운싱이 필요한 경우는 어떤 경우이며, 또 어떻게 적용해야 할까요?

제가 디바운싱을 통해 문제를 해결했었던 경험을 통해 디바운싱에 대해 이야기 해볼까 합니다.

<br>



---

## 문제상황. 'RFID 코드 입력을 처리해야하는데...'

화면에서 RFID 번호를 입력을 받아야 했습니다. RFID란 간단히 말하자면 식별이 가능한 태그입니다. 바코드와 비슷한데, 교통카드처럼 접촉으로 인식하며 상품의 종류 뿐 아니라 각 상품 하나를 인식하며 관리하기 위해 쓰이는 게 보통입니다. 

가령 바코드는 `"삑! 이건 사과`, `삑! 이건 바나나"`로 인식한다면, RFID는 `"삑! 이건 어제 네 번째로 들어온 청송사과`, `삑! 이건 어제 첫 번째로 들어온 충주사과"` 이런 식이죠. 바코드와는 다르게 각 상품에 1:1로 매칭이 되는 것이지요.

{{< figure src="MD_imgs/IMG_0146.png" title="요런 식으로 제품에 붙어있답니다." caption="여기 붙어있는 RFID 값은 저희 집 찬장 구석에 내용물은 모두 내어주고 병만 남은 이 잭다니엘에게만 주어진 고유한 값이죠" width="580px" >}}

자, 그래서 프로젝트 진행 중 RFID 코드를 입력받아야 했습니다. RFID 리더기를 사용해 RFID 태그의 입력을 받았죠. 

RFID 리더기나 바코드 인식기 등, 입력을 받는 기기들은 보통 입력 값을 입력해주고 자동으로 엔터를 자동으로 입력해 처리해줍니다. 그러나 RFID 리더기를 통해서 상품을 읽히는데 예상과는 다르게 입력이 되었습니다. 



---



**기대했던 작동 방식 (희망편)**: RFID 태그를 리더기에 인식 -> RFID 번호 값이 입력된 후 자동으로 엔터 입력 -> 원하는 로직 처리 -> RFID 리더기에 새로운 태그 인식 (반복)

**실제 작동한 방식 (절망편)**: RFID 태그를 리더기에 인식 -> 바로 엔터가 입력되지 않고 근처에 있는 RFID 코드 값이나 방금 인식된 값이 또 입력 -> 엔터 입력 -> 로직 처리 중, 그러나 그 와중에도 입력되고 있는 RFID 번호 -> (서비스) '아 잠깐만여 이 번호는 또 뭐여' -> 그 와중에도 입력되고 있는 RFID -> 으어어 (반복)

---

이런 상황이 발생하고 있었고 실제로 입력된 값들은 다음과 같았습니다.

---
**기대했던 입력값**:

`AAA00000000001` (엔터) -> `AAA00000000002` (엔터) -> `A000000000003` (엔터)

**실제 입력값**:

`AAA00000000001AAA00000000002A00000000` (엔터) *두 개의 온전한 RFID 번호와 불온전한 번호 하나가 결합한 형태*

---
문제를 요약하자면,

1. 하나의 입력 값을 받고 바로 엔터처리가 되지 않음
2. 다른 RFID 태그가 근처에 있으면 같이 입력됨
3. 혹은, 방금 태그한 RFID 태그가 다시 입력 됨
4. 엔터가 입력되고 로직이 처리되는 과정에서 RFID 값이 또 입력 돼, 끝까지 인식되지 않은 RFID 입력 값이 발생

다음의 네 가지와 같았습니다. 따라서 `입력이 완료 되었다고 판단 되었을 때 로직을 실행할 것`, `입력된 RFID 중 중복 값을 제거하고 올바른 RFID만 모아 처리할 것` 의 두 가지 해결 목표가 생겼습니다. 

'흠, 그렇다면 먼저 첫 번째 목표인 `입력이 완료되었을 때...` 를 어떻게 판별하고 처리해야할까...'의 고민 중 문제해결 방법을 찾았고 디바운싱을 적용한다면 이 문제를 해결할 수 있다는 것을 알게 되었습니다.

<br>



---



## 디바운싱. Javascript로 구현하기

Javascript 코드를 통해 디바운싱을 구현해봅시다. 사실 자바스크립트의 라이브러리 `lodash` 에서 `debounce` 메소드를 통해 손쉽게 디바운싱을 사용할 수 있습니다.

코드 원소스: https://www.geeksforgeeks.org/lodash-_-debounce-method/

```javascript
// 출처: GeeksforGeeks

// Requiring lodash library
const _ = require('lodash');

// Using _.debounce() method
// with its parameters
var debounce_fun = _.debounce(function () {
console.log('Function debounced after 1000ms!');
}, 1000);

debounce_fun();
```



여기서는 lodash의 도움을 구하지 않고 직접 코드를 써서 사용해볼까요.



코드 원소스: https://www.geeksforgeeks.org/debouncing-in-javascript/?ref=gcse

```javascript
// 출처: GeeksforGeeks

const debounce = (func, delay) => {
    let debounceTimer
    return function() {
        const context = this
        const args = arguments
        
        clearTimeout(debounceTimer)
        debounceTimer = setTimeout(() => func.apply(context, args), delay)
    }
} 
```



[clearTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/clearTimeout) 메소드는 이전에 생성되어 사용된 [setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout) 을 취소하는 메소드입니다. 디바운싱 함수는 클로저를 이용한 형태로 만들어져있습니다. 따라서 함수 `debounce`의 실행 컨텍스트가 종료된 뒤에도 선언된 변수  `debouncerTimer` 는 사라지지 않고, setTimeout의 delay 시간이 남아있는 상태에서 또 다시 setTimeout의 delay 시간만큼 실행하고자 하는 함수 `func`를 기다리는 대상이 됩니다. 해당 내용은 기회가 된다면 따로 다루는 것이 좋겠네요.



간단하게 디바운싱이 작동하는 페이지를 만들어 봅시다.

{{< figure src="MD_imgs/01_page.png"  width="580px" >}}

input 상자가 있고, 상자에 입력하는 문구가 하단에 기입이 되게 할 것입니다.

텍스트의 입력을 기다리고, 입력이 완료됐다고 판단되면 텍스트가 출력되는 디바운싱을 구현해봅니다.

<br>

{{< figure src="MD_imgs/02_test.gif"  width="580px" >}}

실제는 이렇게 작동합니다. 입력이 완료될 때까지 잘 기다리고 있다가 작동하네요. 상황에 따라서 실시간으로 입력을 받거나, 디바운싱을 받거나 선택해서 사용하면 되겠지요.

코드는 다음과 같습니다.
```html
<!-- index.html -->

<!DOCTYPE html>
<html>
<head>
</head>
<body>
  <main class="App">
  </main>

  <script src="debounce.js" ></script>
</body>
</html>
```

```javascript
// debounce.js

const $app = document.querySelector(".App")
const $inputBox = document.createElement("input")
$app.appendChild($inputBox)
const $textBox = document.createElement("p")
$app.appendChild($textBox)

// 실제 Debouncing 관련 코드
const debounce = (func, delay) => {
  let debounceTimer
  return function() {
      const context = this
      const args = arguments
      
      clearTimeout(debounceTimer)
      debounceTimer = setTimeout(() => func.apply(context, args), delay)
  }
}

// 500ms의 지연시간을 준 debouncing
$inputBox.addEventListener("keydown", debounce(e => {
    $textBox.innerHTML = e.target.value
  }, 500))
```

입력 지연시간은 실제로 서비스에 적용하면서 가장 적절한 값을 찾는 게 좋겠네요. 200, 300ms 혹은 여유있게 1초(1000ms)를 지연하는 것도 상황에 따라 괜찮을 것 같습니다.



다음과 같은 디바운싱으로 RFID 리더기의 입력 값을 기다리고 모든 인식이 끝났을 때 문자열을 입력, 처리해주어 로직을 처리함으로써 문제를 해결할 수 있었습니다.



<br>

---





### +) 비교: Debouncing이 적용되지 않은 경우

디바운싱이 적용되지 않은 경우는 다음과 같습니다.

{{< figure src="MD_imgs/03_difference.gif"  width="580px" >}}

사용자의 입력이 끝나기도 전에, 입력이 될 때마다 함수가 작동하는 것을 알 수 있죠.



<br>

---

## 후일담. 그래서 실제로는 이렇게 적용했어요

디바운싱을 적용해 이제는 불완전한 RFID 번호가 들어오거나, RFID 번호가 들어오고 있는 와중에 의도치 않은 함수가 실행되는 일은 없어졌습니다. 따라서 입력된 값들만 제대로 처리할 수 있게 가공해주기만 하면 끝이었죠.

남아 있는 문제는 다음과 같았습니다.

- 입력된 값은 유효한 RFID 하나의 값, 혹은 다수의 RFID의 값이 포함된 값일 수 있다.
- 입력된 RFID 번호는 같은 값이 중복되어 입력될 수 있다.

실제로 입력된 값의 유형은,

1. 하나의 RFID 번호

   - `ABCDE00000000001` 혹은 `ABCDE00000000002` 와 같은 형태
2. 다수의 RFID 번호

   - `ABCDE00000000001ABCDE00000000002ABCDE00000000003` 과 같은 형태
   - 혹은 `ABCDE00000000001ABCDE00000000002ABCDE00000000001` 과 같이 일부 중복된 형태


다음의 두 가지였습니다.

RFID 번호는 각기 고유한 규격을 가지고 있습니다. 각 RFID 번호는 `고유한 길이`, `첫 문자부터 일정 길이까지 정해진 값`, `특정 값을 가진 분류 값` 등 특정 문자열 패턴을 가지고 있고 유효한 값을 분리해줄 수 있었습니다. 

따라서 **1. 유효한 RFID 값인가**의 경우는, 어차피 별로 길지 않은 문자열이기에 문자열의 글자를 하나하나 탐색하여 길이, 문자에 올바른 위치에 고유번호가 존재하는가(slice 활용) 등으로 해결할 수 있었습니다. 아니면 `정규식` 을 통해 해결할 수 있는 방법도 있겠죠.

**2. 중복된 값이 있는가**는 그냥 RFID의 값으로 인식된 값들을 하나 씩 `Set()`에 넣어주어 해결했습니다. 간단하죠!





<script src="https://utteranc.es/client.js"
        repo="caddyspoon/blog-comments"
        issue-term="pathname"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>
<br>
