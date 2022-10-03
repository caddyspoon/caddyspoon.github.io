# [React] 리액트에서 동적으로 스타일 적용하기


온라인 강의 사이트 udemy에서 **React 완벽 가이드 with Redux, Next.js, TypeScript**를 요즘 수강 중인데, 해당 강의에서 나온 내용을 기반으로 몇 가지 정리해보고자 합니다. 

간단한 예시 화면을 만들어 보겠습니다. 멋진 팬톤의 Baby Blue를 배경색으로, Classic Blue를 버튼 색상으로 사용했습니다.

버튼이 있고 버튼을 누르면 텍스트 색상을 바꾸도록 해볼까요. 변했을 때 색상은 마찬가지로 팬톤 색상의 Rococco Red로 바꾸어 보겠습니다.


{{< figure src="MD_imgs/01.gif" >}}

<br>

<hr>

## 1. 인라인에서 직접 스타일 하기 (Inline Style)

JSX 코드의 style을 직접 변경하는 경우입니다.

`isSwitched` 라는 값을 Boolean 값으로 설정해서, 버튼을 눌렀을 때마다 false/true를 변환해주도록 했습니다.

inline에서 스타일을 직접 지정할 경우 이중괄호를 사용합니다. 바깥 중괄호는 JSX 내 자바스크립트 코드를 사용하기 위한 중괄호이며, 내부 중괄호는 객체 리터럴을 사용하기 위한 중괄호입니다.



전체 코드는 다음과 같습니다.

```jsx
// src/components/StylePrac.js

import React, { useState } from "react";

import "./StylePrac.css";

const StylePrac = () => {
  const [isSwitched, setIsSwitced] = useState(false);
  const switchHandler = () => {
    setIsSwitced(!isSwitched);
  };

  return (
    <div>
      <h1 style={{ color: isSwitched ? "#BB363F" : "#000" }}>
        Make our world colourful!
      </h1>
      <button className="StylePrac__button" onClick={switchHandler}>Switch</button>
    </div>
  );
};

export default StylePrac;
```



여기서 관련된 코드 조각은 이 부분입니다.

```jsx
<h1 style={{ color: isSwitched ? "#BB363F" : "#000" }}>
```

```isSwitched```가 ```true```면 해당 문구를 로코코 레드에 해당하는 ```#BB363F``` 색으로, 그렇지 않으면 검은색으로 색칠됩니다.

<br>

만약 해당 문서의 **기본 글꼴색이 검정색인 경우** 다음과 같이 표현할 수도 있습니다.

```jsx
<h1 style={{ color: isSwitched && "#BB363F" }}>
```

```isSwitched``` 인 경우 로코코 레드 색상이 적용됩니다.

<br>

<hr>

## 2. className을 동적으로 부여

인라인으로 스타일을 부여하는 경우 코드가 스타일에 집중되기 때문에 보기 힘듭니다. 클래스 속성을 동적으로 부여해 스타일을 관리하겠습니다.

이 경우는 템플릿 리터럴을 사용합니다.

클래스로 스타일을 관리할 것이니까, 먼저 해당 클래스 이름을 새롭게 만들면서 css를 작성해야겠죠.

```css
/* StylePrac.css */

/* 기본 색상인 검정색으로 지정합니다. */
.header-text {
  color: #000;
}

/* switched 클래스가 부여되면 아래 색상은 로코코 레드 색상이 적용됩니다. */
.switched {
  color: #BB363F;
}
```

JSX 코드는 다음과 같습니다.

```jsx
<h1 className={`header-text ${isSwitched ? "switched" : ""}`}>
```

isSwitched가 true이면 해당 className은 "header-text switched"가 되고, false면 "header-text"만 남을 겁니다.

또한 다음과 같이 표현해도 같습니다.

```jsx
<h1 className={`header-text ${isSwitched && "switched"}`}>
```

<br>

<hr>

## 3. CSS 모듈 사용

그냥 CSS를 사용하면 해당 클래스 네임의 스타일이 전역으로 적용되기 때문에 해당 컴포넌트에만 적용 가능한 스타일을  사용하는 것이 권장됩니다. CSS 클래스 컨벤션을 ```컴포넌트이름__클래스이름``` 과 같이 지정해줄 수도 있겠지만, 실수가 생길 수도 있죠. 따라서 해당 컴포넌트에 대응하는 스타일만을 적용하도록 해주는 여러가지 방법이 있습니다. 여기서는 CSS 모듈을 사용해보겠습니다.

### 1. module.css 파일 생성

CSS 파일을 CSS 모듈 파일로 만들어줍니다. 파일 확장자를 ```.css```에서 ```.module.css```로 바꾸어주기만 하면 됩니다.

다음과 같은 파일로 변경됩니다. 내용은 같습니다. 확장자면 바뀌네요.

```css
/* StylePrac.module.css */

.header-text {
  color: #000;
}

.switched {
  color: #BB363F;
}
```

<br>

### 2. CSS module 불러오기

해당 컴포넌트의 자바스크립트 코드에서 해당 모듈을 불러옵니다.

```jsx
import React, { useState } from "react";

// 해당 불러오기 대신
// import "./StylePrac.css";

// CSS 모듈 파일을 불러와줍니다.
import style from "./StylePrac.module.css"

...
```

<br>

### 3. style로 내부 속성 사용하기

`style` 을 불러왔기 때문에 먼저 `style`을 접근하고 내부에 있는 속성에 접근합니다. 각 스타일의 경우 JSX 내 자바스크립트 문법으로 표기해주어야 하므로 템플릿 리터럴을 사용합니다.

해당 표기에서는 다른 객체에 접근하는 것처럼 점 표기법(Dot Notation)과 대괄호 표기법(Bracket Notation) 모두 사용할 수 있습니다. 

단, 스타일에서 사용하는 클래스 이름의 경우 케밥 케이스를 사용하는 경우가 많기 때문에, 상황에 맞게 대괄호 표기법을 사용해줍니다.

해당 JSX 코드 조각을 다음과 같습니다.

```jsx
// StylePrac.js
...
<h1 className={`${style["header-text"]} ${isSwitched && style["switched"]}`}>

// style.switched와 같이 점 표기법으로 아래와 같이 표시하는 것도 동일합니다.
// <h1 className={`${style["header-text"]} ${isSwitched && style.switched}`}>
```

<br>

개발자 도구를 통해 해당 HTML을 확인해보면 해당 컴포넌트(여기서는 StylePrac.js)의 고유한 클래스 값이 적용된 것을 확인할 수 있습니다.

{{< figure src="MD_imgs/02.png" caption="색상 네모 내부의 고유 값이 지정되었음을 확인할 수 있습니다." >}}

<br>

해당 컴포넌트의 전체 코드는 다음과 같습니다.

```jsx
import React, { useState } from "react";

import style from "./StylePrac.module.css"

const StylePrac = () => {
  const [isSwitched, setIsSwitced] = useState(false);
  const switchHandler = () => {
    setIsSwitced(!isSwitched);
  };

  return (
    <div>
      <h1 className={`${style["header-text"]} ${isSwitched && style["switched"]}`}>
        Make our world colourful!
      </h1>
      <button className={style["StylePrac__button"]} onClick={switchHandler}>Switch</button>
    </div>
  );
};

export default StylePrac;

```

<hr>





<script src="https://utteranc.es/client.js"
        repo="caddyspoon/blog-comments"
        issue-term="pathname"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>
<br>
