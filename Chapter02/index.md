# 2장 JSX

: 목차

[2.1 코드 이해](#21-코드-이해) 

[2.2 JSX란?](#22-jsx란) 

[2.3 JSX 장점](#23-jsx-장점)

[2.4 JSX 문법](#24-jsx-문법) 

- [2.4.1 감싸인 요소](#241-감싸인-요소) 

- [2.4.2 자바스크립트 표현](#242-자바스크립트-표현) 

- [2.4.3 if 문 대신 조건부 연산자](#243-if-문-대신-조건부-연산자) 

- [2.4.4 &&를 사용한 조건부 렌더링](#244-를-사용한-조건부-렌더링) 

- [2.4.5 인라인 스타일링](#245-인라인-스타일링) 

- [2.4.6 class 대신 className](#246-class-대신-classname) 

- [2.4.7 꼭 닫아야 하는 태그](#247-꼭-닫아야-하는-태그) 

- [2.4.8 주석](#248-주석) 

----------
 
## 2.1 코드 이해

ES6에서는 `import` 라는 키워드를 통해 모듈을 로드한다.

`import React, { Component } from 'react'`

위 구문은 
```javascript
var React = require('react');
var Component = React.Component;
```
이와 같다 볼 수 있다.

`'react'`가 `React` 변수에 할당되고, `{Component}`는 해체 할당을 통해 `Component` 클래스를 로드한 것을 볼 수 있다.

> #####TIP : 해체 할당(ES6)
> 
> ```javascript
> const obj = {a:1,b:2,c:3};
> let {a,b,c} = obj;
> 
> console.log(a) >> 1
> console.log(b) >> 2
> console.log(c) >> 3
> ```

이렇게 파일을 모듈화해서 사용하는 것은 Node.js의 기능이며, 웹 브라우저에서 이와 같은 기능을 수행하기 위해서는 __번들링__ 도구를 이용하는 것입니다.

번들링 도구로는 `Browserify`, `RequireJS`, `webpack` 등이 대표적이며, 이를 통해 `require(또는 import)`로 모듈을 불러왔을 때 번들링되면서 모듈들을 파일 하나로 합쳐줍니다.

_이와 같은 설정은 15장에서 다루고 현재는 `create-react-app` 으로 대신합니다._

다음은 App.js 코드를 확인해 보겠습니다.

```javascript
class App extends Component {
  render() {
      return (
          <div className="App">
            <header className="App-header">
              <img src={logo} className="App-logo" alt="logo" />
              <p>
                Edit <code>src/App.js</code> and save to reload.
              </p>
              <a
                  className="App-link"
                  href="https://reactjs.org"
                  target="_blank"
                  rel="noopener noreferrer"
              >
                Learn React
              </a>
            </header>
          </div>
      );
  }
}
```
해당 App 클래스 내에 render() 함수 내부에서는 컴포넌트를 유저에게 어떻게 보일지 `return` 합니다.

이 내부를 보면 HTML과 비슷한데 작은따옴표나 큰따옴표로 감싸지 않은 것으로 보아 이 코드는 단순한 문자열 템플릿이 아닙니다.
 
이런 코드를 __JSX__ 라 합니다.

> #####TIP : 클래스 문법(ES6)
> 
> ES6 이전에는 클래스를 구현하려면 prototype 이라는 문법을 사용하여 대신 작업해야 했습니다.
> 
> ```javascript
> function Dog(name) {
>     this.name = name;
> }
> Dog.prototype.say = function() {
>     console.log(this,name + ': 멍멍');
> }
> var dog = new Dog('검둥이');
> dog.say();
> 
> >> 검둥이: 멍멍
> ```
> 
> ES6 문법부터는 이것과 기능이 똑같은 코드를 `class`를 사용하여 작성할 수 있습니다.
> 
> ```javascript
> class Dog {
>     constructor(name) {
>         this.name = name;    
>     }
>     say() {
>         console.log(this.name + ': 멍멍');
>     }
> }
> const dog = new Dog('흰둥이');
> dog.say();
> 
> >> 흰둥이: 멍멍
> ```
 
## 2.2 JSX란?

자바스크립트의 확장 문법으로 해당 코드는 나중에 번들링되면서 `babel-loader` 를 사용하여 자바스크립트로 변환합니다.

```javascript
var a = (
    <div>
        <h1>Awesome <b>React</b></h1>
    </div>
)
```

이 코드를 바벨로 변환하면 다음 형식이 됩니다.

```javascript
var a = React.createElement(
    "div",
    null,
    React.createElement(
        "h1",
        null,
        "Awesome ",
        React.createElement(
            "b",
            null,
            "React"
        )
    )
);
```

이렇게 변환하여 컴포넌트를 트리 구조의 객체로 정의할 수 있습니다.

> JSX는 리액트용이기 때문에 공식 자바스크립트 문법은 아닙니다.
 
## 2.3 JSX 장점 

- 보기 쉽고 익숙하다 : HTML과 비슷..

- 오류 검사 : JSX에 오류가 있다면, 바벨이 코드를 변환하는 과정에서 감지해 냅니다. (ex: 태그 안닫혔을 때)

- 더욱 높은 활용도 : div나 span 같은 HTML 태그를 사용할 뿐만 아니라 컴포넌트도 JSX 안에서 작성할 수 있습니다.

> #####TIP : ReactDOM.render란?
> 
> 컴포넌트를 페이지에 렌더링하는 역할을 하며 `react-dom`을 불러와 사용할 수 있습니다.
> 
> 첫번째 파라미터는 __렌더링할 내용을 JSX 형태로 작성__ 하고, 두번째 파라미터는 __해당 JSX를 렌더링 할 document 내부 요소를 설정__ 합니다.

## 2.4 JSX 문법 

JSX는 다음과 같은 규칙을 준수해야 합니다.

### 2.4.1 감싸인 요소 

컴포넌트에 여러 요소가 있다면 __부모 요소 하나로 꼭 감싸야 합니다.__

Virtual DOM에서 컴포넌트 변화를 감지해 낼 때 효율적으로 비교할 수 있도록 컴포넌트 내부는 DOM 트리 구조 하나여야 한다는 규칙이 있기 때문입니다.

리액트 v16 이상부터는 Fragment 컴포넌트가 도입되어 div 같은 것으로 감싸지 않고 리액트를 로드할 시 `Component`와 함께 `Fragment`를 불러와서 다음과 같이 사용하면 됩니다.

```javascript
import React, { Component, Fragment } from 'react';

class App extends Component {
    render() {
        return (
            <Fragment>
                <h1>리액트 안녕!</h1>
                <h2>당신은 어썸한가요?</h2>
            </Fragment>
        );
    }
}

export default App;
```

Fragment를 사용하면 div처럼 감싸서 렌더링되는 것 없이 내부 요소만을 렌더링할 수 있습니다.

### 2.4.2 자바스크립트 표현 

JSX 안에서는 자바스크립트 표현식을 쓸 수 있습니다. 표현식을 작성하려면 코드를 { }로 감싸면 됩니다.

```javascript
import React, { Component } from 'react';

class App extends Component {
  render() {
      const text = '당신은 어썸한가요?';
      return (
            <div>
                <h1>리액트 안녕!</h1>
                <h2>{text}</h2>
            </div>
      );
  }
}

export default App;
```

> #####TIP : const와 let(ES6)
> 
> const : 한번 지정하고 나면 변경이 불가능한 상수를 선언할 때 사용하는 키워드
> 
> let : 동적인 값을 담을 수 있는 변수를 선언할 때 사용하는 키워드
> 
> 기존 var 키워드는 scope(해당 값을 사용할 수 있는 코드 영역)이 함수 단위입니다.
> ```javascript
> function myFunction() {
>     var a = 'hello';
>     if(true) {
>         var a = 'bye';
>         console.log(a); // bye
>     }
>     console.log(a); // bye
> }
> MyFunction();
> ```
> 
> if문 내에서 값을 수정한 것이 외부에서도 값이 변경되어 출력되는데 이런 결점을 해결해 주는 것이 let과 const입니다.
> 
> let과 const는 블록 단위이므로 if 문 내부에서 선언한 a 값은 if 문 밖의 a 값을 변경하지 않습니다.
> 
> ```javascript
> function myFunction() {
>     let a = 1;
>     if(true) {
>         let a = 2;
>         console.log(a); // 2
>     }
>     console.log(a) // 1
> }
> ```
> 
> let과 const를 사용할 때 같은 __블록 내부에서 중복 선언이 불가능__ 하다는 점에 주의하세요.
> 
> 또한 const는 __한번 선언 시 재설정할 수 없습니다.__

### 2.4.3 if 문 대신 조건부 연산자 

JSX 내부의 자바스크립트 표현식에서 if 문은 사용할 수 없습니다. 대신 조건부 (삼항) 연산자를 사용하면 됩니다.

`{condition ? '참': '거짓'}`

### 2.4.4 &&를 사용한 조건부 렌더링

JSX 에서는 null, false 등 값을 렌더링하면 아무것도 나타나지 않습니다.

따라서 특정 조건을 만족할 때 노출하고 아닌 경우 미노출인 경우에는 삼항 연산자 대신 && 연산자로 조건부 렌더링을 할 수 있습니다.

`{ condition && '보여주세요' }`
 
### 2.4.5 인라인 스타일링 

DOM 요소에 스타일을 적용할 때는 자바스크립트 객체 형식으로 만들어 적용해야 합니다.

해당 __객체의 key는 camelCase로 작성__ 합니다.

```javascript
const style = {
    backgroundColor: 'gray',
    border: '1px solid black',
    height: Math.round(Math.random() * 30) + 50,
    width: Math.round(Math.random() * 30) + 50,
    WebkitTransition: 'all',
    msTransition: 'all',
    transition: 'all'
}
```

위처럼 웹 브라우저 접두어인 __-moz, -webkit 등은 Moz, Webkit 으로 바꾸어 사용하되, -ms는 예외로 대문자로 바꾸지 않고 소문자 ms로 작성__ 합니다.

### 2.4.6 class 대신 className 

기존 class 문법을 사용하고 있기 때문에 요소에 들어가는 class는 className으로 수정하여야 합니다.

```javascript
class App extends Component {
    render() {
        return (
            <div className="my-div">
                <h1>리액트 안녕!</h1>
                <h2>{text}</h2>
            </div>
        )
    }
}
```

### 2.4.7 꼭 닫아야 하는 태그 

input, br 처럼 닫지 않아도 되었던 태그들은 JSX에서는 __무조건 닫아야 합니다.__

```html
<form>
    First name:<br/>
    <input type="text" name="firstname"/><br/>
    Last name:<br/>
    <input type="text" name="lastname"/>
</form>
```

### 2.4.8 주석 

```javascript
return (
    <div className="my-div">
        { /* 요소 밖에서는 이렇게 작성해요. */ }
        <h1></h1>
        <h2></h2>
        { condition && '보여주세요' }
        <div style={style}
            // self-closed 태그에서만 작동하는 주석
            // 마지막 />가 꼭 새 줄에 있어야 합니다.
            /* 이렇게 작성할 수도 있고요. */
        />
        // 여기 쓰는 건 그대로 렌더링 됩니다.
        /* 여기에선 주석 못 써요 */
    </div>
);
```
일반적으로 __주석을 작성할 시 `{ /* 내용 */ }` 으로 작성__ 합니다.

self-closed 요소(`<div></div>` 가 아닌 `<div/>`처럼 자체적으로 닫는 요소를 의미) 내에서는 주석을 `{ }` 없이 작성할 수 있습니다.