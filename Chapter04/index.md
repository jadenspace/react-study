# Chapter4 이벤트 핸들링

## 4.1 리액트 이벤트 시스템

리액트의 이벤트 시스템은 웹 브라우져 HTML 이벤트와 인터페이스가 동일하기 때문에 사용법이 꽤 비슷합니다. 다만 <u>몇 가지 주의해야 할 사항</u>이 있습니다.

1. 이벤트 이름은 camelCase로 작성<br>
   예를 들어 onclick 이벤트는 onClick / onkeyup은 onKeyUp으로 작성
2. 자바스크립트 코드로 전달이 아닌, 함수 형태의 값으로 전달<br>
   HTML에서 이벤트를 설정할 때는 "" 또는 '' 안에 실행할 코드를 작성하였지만, 리액트는 함수 형태의 객체를 전달
3. DOM 요소에만 이벤트를 설정 할 수 있음<br>
   div, button, input 등 DOM 요소에는 이벤트를 설정할 수 있지만, 직접 만든 컴포넌트에는 이벤트를 자체적으로 설정할 수 없음. 단 전달받은 props를 컴포넌트 내부의 DOM 이벤트로는 설정 가능 


### 이벤트 종류
    - Clipboard                 - Form
    - Composition               - Mouse
    - Keyboard                  - Selection
    - Focus                     - Touch
    - UI                        - Image
    - Wheel                     - Animation
    - Media                     - Transition

리액트 매뉴얼(https://reactjs.org/docs/events.html) 참고

## 4.2 이벤트 핸들링

### 4.2.1 onClick 이벤트

```
<button onClick={
    () => {
        alert('test');
    }
}>버튼</button>
```

### 4.2.2 onChange 이벤트

```
<input
    type="text"
    name="message"
    placeholder="값을 입력하세요."
    onChange={
        (e) => {
            console.log(e.target.value)
        }
    }
/>
```

### 4.2.3 state에 input value 담기

```
state = {
    message : ''
}

<input
    type="text"
    name="message"
    placeholder="값을 입력하세요."
    value={this.state.message}
    onChange={
        (e) => {
            this.setState({
                message : e.target.value
            })
        }
    }
/>

<button onClick={
    () => {
        alert(this.state.message)
        this.setState({
            message: ''
        })
    }
}>Check</button>
```

### 4.2.4 메서드 만들기
```
import React, { Component }  from 'react';

class EventPractice extends Component {

    state = {
        message : ''
    }

    constructor(props) {
        super(props);
        this.handleChange = this.handleChange.bind(this);
        this.handleClick = this.handleClick.bind(this);
    }

    handleChange(e) {
        this.setState({
            message: e.target.value
        });
    }

    handleClick() {
        alert(this.state.message);
        this.setState({
            message: ''
        });
    }

    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                
                <input
                    type="text"
                    name="message"
                    placeholder="입력하세요."
                    value={this.state.message}
                    onChange={this.handleChange}
                />
                <button onClick={this.handleClick}>클릭</button>
            </div>
        );
    }
}

export default EventPractice;
```

### 4.2.5 Property Initializer Syntax를 사용한 메서드
메서드 바인딩은 생성자 메서드에서 하는 것이 정석입니다. 하지만 새 메서드를 생성할 때마다 constructor도 수정해야 하기 때문에 불편하다고 느낄 수 있습니다. 이 작업을 좀 더 간단하게 하기 위해서는 바벨 transform-class-properties 문법을 사용하여 화살표 함수(Arrow Function) 형태로 메서드를 정의하는 것입니다.

```
import React, { Component }  from 'react';

class EventPractice extends Component {

    state = {
        message : ''
    }

    handleChange = (e) => {
        this.setState({
            message: e.target.value
        });
    }

    handleClick = () => {
        alert(this.state.message);
        this.setState({
            message: ''
        });
    }

    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                
                <input
                    type="text"
                    name="message"
                    placeholder="입력하세요."
                    value={this.state.message}
                    onChange={this.handleChange}
                />
                <button onClick={this.handleClick}>클릭</button>
            </div>
        );
    }
}

export default EventPractice;
```

### 4.2.6 다중 input 핸들링
input이 여러 개 있는 경우 여러 개의 메서드를 만드는 방법도 있지만, event 객체를 활용하는 방법도 있습니다.
e.target.name 값을 사용하면 됩니다.
```
import React, { Component }  from 'react';

class EventPractice extends Component {

    state = {
        username: '',
        message: ''
    }

    handleChange = (e) => {
        this.setState({
            [e.target.name]: e.target.value
        });
    }

    handleClick = () => {
        alert(this.state.username + ': ' + this.state.message);
        this.setState({
            username: '',
            message: ''
        });
    }

    render() {
        return (
            <div>
                <h1>이벤트 연습</h1>
                <input
                    type="text"
                    name="username"
                    placeholder="유저명"
                    value={this.state.username}
                    onChange={this.handleChange}
                />
                <input
                    type="text"
                    name="message"
                    placeholder="입력하세요"
                    value={this.state.message}
                    onChange={this.handleChange}
                />
                <button onClick={this.handleClick}>확인</button>
            </div>
        );
    }
}

export default EventPractice;
```