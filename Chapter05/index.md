# Chapter5 ref: DOM에 이름 달기

## 5.1 ref는 어떤 상황에서 사용해야 할까?

1. input/textarea 등에 포커스를 하는 경우
2. 특정 DOM의 크기를 가져와야 하는 경우
3. 특정 DOM에서 스크롤 위치를 가져오거나 설정을 하는 경우
4. 외부 라이브러리 등을 사용하는 경우

## 5.2 ref 사용

ref를 달아야 하는 DOM에서 ref 속성을 추가할 때는 props를 설정하듯이 하면 됩니다.<br>
ref 값으로는 콜백 함수를 전달합니다. 콜백 함수는 ref를 파라미터로 가지며, 콜백 함수 내부에서 컴포넌트의 멤버 변수에 ref를 담는 코드를 작성합니다.


### 5.2.1 ref 사용 방법
```
<input ref={ (ref) => {this.input=ref} } />
```

### 5.2.2 ref 예제
App.js

```
import React, { Component }  from 'react';
import ValidationSample from './ValidationSample';

class App extends Component {
	render() {
		return (
			<div>
				<ValidationSample />
			</div>
		);
	}
}

export default App;
```

ValidationsSample.js
```
import React, { Component } from 'react';
import './ValidationSample.css';

class ValidationSample extends Component {
    state = {
        password: '',
        clicked: false,
        validated: false
    }

    handleChange = (e) => {
        this.setState({
            password: e.target.value
        });
    }

    handleBtnClick = (e) => {
        this.setState({
            clicked: true,
            validated: this.state.password === '0000'
        });
        this.input.focus();
    }

    render() {
        return (
            <div>
                <input
                    type="password"
                    value={this.state.password}
                    onChange={this.handleChange}
                    className={this.state.clicked ? (this.state.validated ? 'success' : 'failure') : ''}
                    ref={ (ref) => this.input = ref }
                />
                <button onClick={this.handleBtnClick}>검증</button>
            </div>
        );
    }
}

export default ValidationSample;
```

ValidationsSample.css
```
body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
    "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

code {
  font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
    monospace;
}

```

## 5.3 컴포넌트에 ref 달기

컴포넌트 내부에 있는 DOM을 컴포넌트 외부에서 사용할 때 사용합니다. 컴포넌트에 ref를 다는 방법은 DOM에서 ref를 다는 방법과 동일합니다.

```
<Component ref={ (ref) => {this.Component} }/>
```

### 5.3.1 컴포넌트 ref 예제

App.js
```
import React, { Component }  from 'react';
import ScrollBox from './ScrollBox';

class App extends Component {
	render() {
		return (
			<div>
				<ScrollBox ref={ (ref) => this.scrollBox = ref } />
				<button onClick={ () => this.scrollBox.scrollToBottom() }>밑으로</button>
			</div>
		);
	}
}

export default App;
```

ScrollBox.js
```
import React, { Component } from 'react';

class ScrollBox extends Component {

    scrollToBottom = () => {
        const {scrollHeight, clientHeight} = this.box;
        this.box.scrollTop = scrollHeight - clientHeight;
    }
    
    render() {
        const style = {
            overflow: 'auto',
            position: 'relative',
            width: '300px',
            height: '300px',
            border: '1px solid #000',

        }

        const innerStyle = {
            width: '100%',
            height: '650px',
            background: 'linear-gradient(#FFF, #000)'
        }
        
        return (
            <div
                style={ style }
                ref={ (ref) => {this.box=ref} }>
                <div style={innerStyle}/>
            </div>
        );
    }
}

export default ScrollBox;
```

* ES6 <a href="https://www.zerocho.com/category/ECMAScript/post/575d20a97d96d81700508ccd">비구조화 할당(Destructruing Assignment)</a>