# 3장 컴포넌트

: 목차

[3.1 첫 컴포넌트 생성](#31-첫-컴포넌트-생성) 

[3.2 props](#32-props) 

- [3.2.1 JSX 내부에서 props 렌더링](#321-jsx-내부에서-props-렌더링)

- [3.2.2 컴포넌트 사용할 때 props 값 설정](#322-컴포넌트-사용할-때-props-값-설정)

- [3.2.3 props 기본 값 설정 defaultProps](#323-props-기본-값-설정-defaultprops)

- [3.2.4 props 검증: propTypes](#324-props-검증-proptypes)

    - [3.2.4.1 필수 propTypes 설정](#3241-필수-proptypes-설정)

    - [3.2.4.2 더 많은 PropTypes 종류](#3242-더-많은-proptypes-종류)

[3.3 state](#33-state)

- [3.3.1 컴포넌트의 생성자 메서드: constructor()](#331-컴포넌트의-생성자-메서드-constructor)

- [3.3.2 state 초깃값 설정](#332-state-초깃값-설정)

- [3.3.3 JSX 내부에서 state 렌더링](#333-jsx-내부에서-state-렌더링)

- [3.3.4 state 값 업데이트: setState()](#334-state-값-업데이트-setstate)

- [3.3.5 state를 constructor에서 꺼내기](#335-state를-constructor에서-꺼내기)

- [3.3.6 state 값을 업데이트할 때 주의 사항](#336-state-값을-업데이트할-때-주의-사항)

----------
 
## 3.1 첫 컴포넌트 생성

- 첫 컴포넌트 생성 : 파일 만들기 > 초기 코드 작성하기 > 모듈 내보내고 불러오기

```javascript
import React, { Component } from 'react';

class MyComponent extends Component {
    render() {
        return (
            <div>나의 새롭고 멋진 프로젝트</div>
        )
    }
}
```

초기 코드 작성을 하고 모듈 내보내기(export)를 위해 마지막에 해당 구문을 삽입합니다.

`export default MyComponent`

App 컴포넌트에서 MyComponent 컴포넌트를 불러와 사용해 봅시다.

```javascript
import React, { Component } from 'react';
import MyComponent from './MyComponent';

class App extends Component {
    render() {
        return (
            <MyComponent/>
        )
    }
}

export default App;
```

## 3.2 props

props는 properties를 줄인 표현으로 __컴포넌트 속성을 설정할 때 사용하는 요소__ 입니다.

해당 컴포넌트를 불러와 사용하는 부모 컴포넌트에서만 props 값을 설정할 수 있습니다.

- props 값 설정 및 검증 : props 렌더링하기 > props 값 설정하기 > props 기본 값 설정하기 > props 값 검증하기

### 3.2.1 JSX 내부에서 props 렌더링

props를 렌더링 할 때는 JSX 내부에서 `{ }` 안에 감싸주면 됩니다.

```javascript
import React, { Component } from 'react';

class MyComponent extends Component {
    render() {
        return (
            <div>
                안녕하세요, 제 이름은 {this.props.name} 입니다.
            </div>
        )
    }
}

export default MyComponent;
```

### 3.2.2 컴포넌트 사용할 때 props 값 설정

MyComponent의 `name` 속성을 설정하려는 경우 HTML 태그에 속성을 설정하는 것과 비슷합니다.

```javascript
import React, { Component } from 'react';
import MyComponent from './MyComponent';

class App extends Component {
    render() {
        return (
            <MyComponent name="React"/>
        )
    }
}

export default App;
```

### 3.2.3 props 기본 값 설정 defaultProps

`name`을 지정하지 않았을 시 생략이 되는데 이를 기본값 설정을 통해 제어할 수 있습니다.

```javascript
import React, { Component } from 'react';

class MyComponent extends Component {
    (...)
}

MyComponent.defaultProps = {
    name: '기본 이름'
}

export default MyComponent;
```

또 다른 방법으로 클래스 내부에서 정의하는 것이 있는데 ES6 stage-2에서 소개한 `transform-class-properties` 문법으로 사용 가능합니다.

`create-react-app`으로 만든 프로젝트는 이 문법을 적용하기 때문에 별도 작업은 없습니다.

```javascript
import React, { Component } from 'react';

class MyComponent extends Component {
    static defaultProps = {
        name: '기본 이름'
    }
    render(){
        return (
            <div>
                안녕하세요, 제 이름은 {this.props.name} 입니다.
            </div>
        )
    }
}

export default MyComponent;
```

### 3.2.4 props 검증: propTypes

컴포넌트의 필수 props를 지정하거나 props 타입을 지정할 때는 propTypes를 사용합니다.

우선 코드 위쪽에 propTypes를 불러옵니다.

`import PropTypes from 'prop-types';`

그 이후에는 클래스 밖에서 설정하거나 `transform-class-properties` 문법을 사용해서 설정합니다.
```javascript
import React, { Component } from 'react';
import PropTypes from 'prop-types';

class MyComponent extends Component {
    static defaultProps = {
        name: '기본 이름'
    }
    
    static propTypes = {
        name: PropTypes.string // name props 타입을 문자열로 설정합니다.
    }
    
    render(){
        return (
            <div>
                안녕하세요, 제 이름은 {this.props.name} 입니다.
            </div>
        )
    }
}

export default MyComponent;
```

name props 타입을 문자열로 설정했습니다. App 컴포넌트의 MyComponent name 값으로 문자열이 아닌 숫자를 전달해봅니다.

렌더링은 되었지만 콘솔에 오류메시지를 출력하게 됩니다.

#### 3.2.4.1 필수 propTypes 설정

props를 지정하지 않았을 때 오류창을 띄우도록 설정해봅니다.

propTypes를 설정 시 `isRequired`를 붙여주면 됩니다.

```javascript
import React, { Component } from 'react';
import PropTypes from 'prop-types';

class MyComponent extends Component {
    static defaultProps = {
        name: '기본 이름'
    }
    
    static propTypes = {
        name: PropTypes.string, // name props 타입을 문자열로 설정합니다.
        age: PropTypes.number.isRequired // 필수적으로 존재해야 하며 숫자입니다.
    }
    
    render(){
        return (
            <div>
                <p>안녕하세요, 제 이름은 {this.props.name} 입니다.</p>
                <p>저는 {this.props.age}살 입니다.</p>
            </div>
        )
    }
}

export default MyComponent;
```

age값이 설정되어 있지 않은 경우에 오류메시지를 출력합니다.

`<MyComponent name="React" age={4}/>`

다음을 추가하면 props 값을 제대로 설정해 줍니다.

#### 3.2.4.2 더 많은 PropTypes 종류

|종류|설명|
|:---|:---|
|array|배열|
|bool|참,거짓|
|func|함수|
|number|숫자|
|object|객체|
|string|문자열|
|symbol|ES6 문법의 심벌 개체|
|node|렌더링할 수 있는 모든 숫자(숫자, 문자열, element 또는 이들로 구성된 배열)|
|element|리액트 요소|
|instanceOf|특정 클래스의 인스턴스|
|oneOf(['Male', 'Female'])|주어진 배열 요소 중 값 하나|
|oneOfType([React.PropTypes.string, React.PropTypes.number])|주어진 배열 안의 종류 중 하나|
|arrayOf(React.PropTypes.number)|주어진 종류로 구성된 배열|
|objectOf(React.PropTypes.number)|주어진 종류의 값을 가진 객체|
|shape({name: React.PropTypes.string, age: React.PropTypes.number})|주어진 스키마를 가진 객체|
|any|아무 종류|

이 키워드들은 PropTypes.bool 또는 PropTypes.any.isRequired 같은 방식으로 사용하면 됩니다.

## 3.3 state

컴포넌트 내부에서 읽고 또 업데이트할 수 있는 값을 사용하려면 `state`를 써야 합니다.

`state`는 언제나 기본 값을 미리 설정해야 사용할 수 있으며, `this.setState()` 메서드로만 값을 업데이트해야 합니다.

- state 값 설정 및 업데이트 : state 초깃값 설정하기 > state 렌더링하기 > state 값 업데이트하기

### 3.3.1 컴포넌트의 생성자 메서드: constructor()

state 초깃값은 컴포넌트 생성자 메서드인 `constructor` 내부에서 설정합니다.

```javascript
import React, { Component } from 'react';
import PropTypes from 'prop-types';

class MyComponent extends Component {
    (...)
    constructor(props) {
        super(props);
    }
    
    render() {
        (...)
    }
}

export default MyComponent;
```

MyComponent 클래스의 constructor 메서드를 만들어 주지 않으면 Component 클래스의 생성자 메서드를 그대로 사용합니다.

직접 constructor 메서드를 작성하여 생성자 메서드에서 추가 작업을 하려면 메서드 내부에서 부모 클래스인 Component의 constructor 메서드를 먼저 호출해야합니다.
 
이때 `super` 키워드를 사용합니다.

컴포넌트를 만들 때 props 값들을 사용하므로 props를 메서드의 파라미터로 전달합니다.

### 3.3.2 state 초깃값 설정

constructor 메서드 안에서 state의 초깃값을 지정해 봅니다.

```javascript
constructor(props) {
    super(props);
    this.state = {
        number: 0
    }
}
```

### 3.3.3 JSX 내부에서 state 렌더링

state 렌더링은 prop 렌더링과 비슷합니다.

```javascript
render() {
    return (
        <div>
            <p>안녕하세요, 제 이름은 {this.props.name} 입니다.</p>
            <p>저는 {this.props.age}살 입니다.</p>
            <p>숫자 {this.state.number}</p>
        </div>
    )
}
```

### 3.3.4 state 값 업데이트: setState()

state값을 업데이트 할 때에는 `this.setState()` 메서드를 사용합니다.

```javascript
render() {
    return (
        <div>
            <p>숫자: {this.state.number}</p>
            <button onClick={() => {
                this.setState({
                    number: this.state.number + 1
                })
            }}>더하기</button>
        </div>
    )
}
```

버튼을 누를때마다 숫자 값이 1씩 증가하도록 설정한 예제입니다.

리액트의 이벤트 시스템은 4장에서 자세히 알아볼 것입니다.

> ##### TIP : 화살표 함수(ES6)
> 
> 함수를 표현하는 새로운 방식이며, 기존 function을 이용한 방식을 대체하기보다는 각각 맞는 상황에 사용합니다.
> 
> ```javascript
> function Dog(){
>     this.name = '흰둥이';
>     return {
>         name: '검둥이',
>         bark1: function() {
>             console.log(this.name + ': 멍멍!');
>         },
>         bark2: () => {
>             console.log(this.name + ': 멍멍!');
>         }
>     }
> }
> 
> const Dog = new Dog();
> Dog.bark1(); // 검둥이: 멍멍!
> Dog.bark2(); // 흰둥이: 멍멍!
> ``` 
> 일반 함수는 자신이 종속된 개체를 this로 가리키며, 화살표 함수는 자신이 종속된 인스턴스를 가리킵니다.

[> 화살표 함수 더 자세히 알아보기](https://github.com/jadenspace/JEON206/blob/master/Chapter6/index.md#67-%ED%99%94%EC%82%B4%ED%91%9C-%ED%91%9C%EA%B8%B0%EB%B2%95)

### 3.3.5 state를 constructor에서 꺼내기

초기 state는 constructor 메서드에서 정의해야 하지만, defaultProps와 propTypes를 정의할 때 사용한 `transform-class-properties` 문법으로 바깥에 정의할 수 있습니다.

```javascript
import React, { Component } from 'react';
import PropTypes from 'prop-types';

class MyComponent extends Component {
    static defaultProps = {
        name: '기본 이름'
    }
    
    static propTypes = {
        name: PropTypes.string,
        age: PropTypes.number.isRequired
    }
    
    state = {
        number: 0
    }
    
    render() {
        (...)
    }
}

export default MyComponent;
```

### 3.3.6 state 값을 업데이트할 때 주의 사항

`state` 값을 업데이트할 때는 `.setState`로만 업데이트 해야 합니다. 다음은 잘못된 코드입니다.

```javascript
this.state.number = this.state.number + 1;
this.state.someArray.push(3);
this.state.someObject.value = 3;
```

- setState() 의 역할 : 파라미터로 전달받은 필드를 업데이트 > 컴포넌트 리렌더링되도록 트리거

위처럼 직접 수정하는 경우 컴포넌트를 자동으로 리렌더링하지 않습니다.

`this.forceUpdate()` 를 통한 강제 리렌더링 기능이 있지만 비효율적이므로 사용을 피해야 합니다.

배열이나 객체를 업데이트할 때는 사본 생성 > 사본 값 업데이트 > 사본으로 값 설정 방식으로 진행하는데 이는 8장에서 자세히 다룹니다.