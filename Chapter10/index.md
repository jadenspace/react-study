# 10장 일정 관리 웹 애플리케이션 생성

: 목차

[10.1 프로젝트 준비](#10.1-프로젝트-준비) 

[10.2 UI 디자인 및 구성](#10.2-UI-디자인-및-구성) 

[10.3 상태 관리](#10.3-상태-관리)

[10.4 정리](#10.4-정리)

----------
 
##10.1 프로젝트 준비

###10.1.1 create-react-app을 이용한 프로젝트 생성
```javascript
$ create-react-app todo-list
```

### 10.1.2 CSS Module 및 Sass 적용
####10.1.2.1 프로젝트 환경설정 eject
```javascript
$ yarn eject 또는 $ npm run eject
```
####10.1.2.2 Sass관련 모듈과 classnames 설치
```javascript
$ yarn add sass-loader node-sass classnames 또는 $ npm install sass-loader node-sass classnames
```
####10.1.2.3 webpack 설정 파일 수정
- webpack.config.js 에 sass-loader 설정 추가
- config/paths.js 에 styles 디렉터리 정의
```javascript
module.exports = {
  (...),
  styles : resolveApp("src/styles")
};
```

#### 10.1.2.4 open-color 적용
```javascript
$ yarn add open-color 또는 $ npm install open-color
```
open-color 라이브러리 호출

경로 => src/styles/util.scss
 ```scss
 @import "~open-color/open-color";
 ```

#### 10.1.2.5 main 스타일 설정
 경로 => src/styles/main.scss
 ```scss
 @import "utils";
body {
  background : $oc-gray-1;
  margin : 0px;
}
 ```
 index.js에서 해당 main.scss 호출
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import './styles/main.scss';
import App from "./components/App.js"
import * as serviceWorker from './serviceWorker';
 
ReactDOM.render(<App />, document.getElementById('root'));
 
serviceWorker.unregister();
```

#### 10.1.2.6 App 컴포넌트 생성 후 webpack 개발 서버 시작

=> App.js
```javascript
import React, { Component } from "react";
 
class App extends Component {
  render(){
    return (
      <div>
        일정관리
      </div>
     )
  }
}
 
export default App;
```
```javascript
$ yarn start 또는 $ npm start
```

## 10.2 UI 디자인 및 구성

###10.2.1 컴포넌트 계획

#### 10.2.1.1 PageTemplate
- 유저 인터페이스의 전체적인 틀
- Title 및 컴포넌트들이 children 값으로 존재

#### 10.2.1.2 TodoInput
- 일정 추가 시 사용하는 input 컴포넌트
- <추가> 버튼 내장되어있음

#### 10.2.1.3 TodoItem
- 각각의 일정 컴포넌트
- 완료 여부를 판단하는 체크박스 및 지우기 버튼으로 구성

#### 10.2.1.4 TodoList
- 일정 List 컴포넌트
- TodoItem 컴포넌트로 구성

### 10.2.2 PageTemplate 컴포넌트 생성
- src/components/PageTemplate/PageTemplate.js
```javascript
import React from "react";
import styles from "./PageTemplate.scss";
import classNames from "classnames/bind";
 
const cx = classNames.bind(styles);
 
/* 페이지 템플릿을 위한 컴포넌트 입니다. 페이지의 틀,
    그리고 타이틀 / 컨텐츠 등 속성이 설정되어 있습니다. */
const PageTemplate = ({children}) => {
  return(
    <div className={cx("page-template")}>
      <h1>일정 관리</h1>
      <div className={cx("content")}>
          {children}
      </div>
    </div>
  )
}
 
export default PageTemplate;
```
- src/components/PageTemplate/PageTemplate.scss
```scss
.page-template {
  margin-top: 5rem;
  margin-left: auto;
  margin-right: auto;
  width: 500px;
  background: white;
  box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);
  padding-top: 2rem;

  @media(max-width: 768) {
    margin-top: 1rem;
    width: calc(100% - 2rem);
  }
  h1{
    text-align: center;
    font-size: 4rem;
    font-weight: 300;
    margin: 0;
  }
  .content {
    margin-top: 2rem;
  }
}
```
- src/components/PageTemplate/index.js
> App 컴포넌트에서 PageTemplate 컴포넌트를 불러오기 위해 경로 입력 시 
> './PageTemplate/PageTemplate'이 아닌 './components/PageTemplate'이라고 입력하기 위함.
```javascript
export { default } from './PageTemplate';
```
- src/components/App.js
> App 컴포넌트에서 PageTemplate 렌더링
```javascript
import React, { Component } from "react";
import PageTemplate from "./PageTemplate";
 
class App extends Component {
  render(){
    return (
      <PageTemplate>안녕하세요.</PageTemplate>
    )
  }
}
```

### 10.2.3 TodoInput 컴포넌트 생성
- src/components/TodoInput/TodoInput.js
```javascript
import React from "react";
import styles from "./TodoInput.scss"
import classNames from "classnames/bind";
 
const cx = classNames.bind(styles);
 
/**
 * @param value : input 값
 * @param onChange : input 변경 이벤트
 * @param onInsert : 추가 버튼 클릭 이벤트
 */
const TodoInput = ({value, onChange, onInsert}) => {
  const handleKeyPress = (e) => {
    if(e.key === "Enter") {
      onInsert();
    }
  };
 
  return (
    <div className={cx("todo-input")}>
      <input onChange={onChange} value={value} onKeyPress={handleKeyPress}/>
      <div className={cx("add-button")} onClick={onInsert}>추가</div>
    </div>
  )
}
 
export default TodoInput;
```
- src/components/TodoInput/TodoInput.scss
```scss
@import '../../styles/utils';

.todo-input{
  border-top: 1px solid $oc-gray-2;
  border-bottom: 1px solid $oc-gray-2;
  display: flex;
  padding: 1rem;
  input {
    flex: 1;
    font-size: 1.1rem;
    outline: none;
    border: none;
    background: transparent;
    border-bottom: 1px solid $oc-gray-4;
    &:focus {
      border-bottom: 1px solid $oc-cyan-6;
    }
  }
  .add-button {
    width: 5rem;
    height: 2rem;
    margin-left: 1rem;
    border: 1px solid $oc-green-7;
    color: $oc-green-7;
    font-weight: 500;
    font-size: 1.1rem;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    &:hover {
      background: $oc-green-7;
      color: white;
    }
    &:active {
      background: $oc-green-8;
    }
  }

}
```
- src/components/TodoInput/index.js
```javascript
export { default } from './TodoInput';
```
- src/components/App.js
> App 컴포넌트에서 PageTemplate 렌더링
```javascript
import React, { Component } from "react";
import PageTemplate from "./PageTemplate";
import TodoInput from "./TodoInput";
 
class App extends Component {
  render(){
    return (
      <PageTemplate>
        <TodoInput/>
      </PageTemplate>
    )
  }
}
```
### 10.2.4 TodoItem 컴포넌트 생성
> - 라이프사이클 API 사용해야하기에 class 문법 사용
> - class 문법 사용하므로 파라미터가 아닌 props로 받아옴

>   done => 해당 일정의 완료 여부

>   children => 일정 내용

>   onToggle => 일정 완료 상태에 대한 on/off함수

>   onRemove => 해당 일정을 제거 하는 함수
- src/components/TodoItem/TodoItem.js
```javascript
import React,  { Component } from "react";
import styles from "./TodoItem.scss";
import classNames from "classnames/bind";
 
const cx = classNames.bind(styles);
 
class TodoItem extends Component {
  render(){
    const {done, children, onToggle, onRemove} = this.props;
 
    return (
      <div className={cx("todo-item")} onClick={onToggle}>
        <input className={cx("tick")} type="checkbox" checked={done} readOnly/>
        <div className={cx("text", { done })}>{children}</div>
        <div className={cx("delete")} onClick={onRemove}>[지우기]</div>
      </div>
    )
  }
}
 
export default TodoItem;
```
 `const {done, children, onToggle, onRemove} = this.props;`

위와 같이 비구조화 할당 (= 해체할당 = destructuring assignment)을 함으로서 

this.props.onToggle, this.props.onRemove 처럼 매번 this.props를 붙이는 것을 생략 가능함.

 `<div className={cx("delete")} onClick={onRemove}>[지우기]</div>`
  
 상위 div의 onClick 함수와 겹쳐있을 경우 하위의 이벤트부터 실행됨.
 
 즉, onRemove -> onToggle 함수가 실행되기때문에 onRemove 함수가 정상 작동 하지 않음.
 
- src/components/TodoItem/TodoItem.scss
```scss
@import "../../styles/utils";

.todo-item {
  padding: 1rem;
  display: flex;
  align-items: center;
  cursor: pointer;
  .tick {
    margin-right: 1rem;
  }
  .text {
    flex: 1;
    word-break: break-all;
    &.done {
      text-decoration: line-through;
    }
  }
  .delete {
    margin-left: 1rem;
    color: $oc-red-7;
    font-size: 0.8rem;
    &:hover {
      color: $oc-red-5;
      text-decoration: underline;
    }
  }
  &:nth-child(odd) {
    // 홀수 번째 요소에는 회색 배경
    background: $oc-gray-0;
  }
  &:hover {
    background: $oc-gray-1;
  }
}

.todo-item + .todo-item {
  // 컴포넌트 사이에 위쪽 테두리를 설정합니다.
  border-top: 1px solid $oc-gray-1;
}
```
- src/components/TodoInput/index.js
```javascript
export { default } from './TodoItem';
```

### 10.2.5 TodoList 컴포넌트 생성
> 데이터 배열을 컴포넌트 배열로 변환하여 랜더링 하는 역할

- src/components/TodoList/TodoList.js
```javascript
import React, { Component } from "react";
import TodoItem from "../TodoItem";

class TodoList extends Component {
  render(){
    return (
      <div>
        <TodoItem done>리액트 공부하기</TodoItem>
        <TodoItem>컴포넌트 스타일링 해보기</TodoItem>
      </div>
    )
  }
}

export default TodoList;
```

- src/components/TodoList/index.js
```javascript
export { default } from './TodoList';
```

- src/components/App.js
```javascript
import React, { Component } from "react";
import PageTemplate from "./PageTemplate";
import TodoInput from "./TodoInput";
import TodoList from "./TodoList";
 
class App extends Component {
  render(){
    return (
      <PageTemplate>
        <TodoInput/>
        <TodoList/>
      </PageTemplate>
    )
  }
}
```
## 10.3 상태 관리
> 개발을 할 때 상태 관리는 주로 기능별로 상태가 필요한 컴포넌트들을 감싸는 상위 컴포넌트에서 하는 것이 더 편리함.
> 즉, TodoInput, TodoList 컴포넌트를 감싸는 상위 컴포넌트 App에서 하도록 함.

`좋지 않은 예`
> 데이터 배열의 state를 TodoList 컴포넌트에서 정의하고, TodoInput의 state를 그 내부에서 정의 했다고 가정함.
>
> if... 신규 데이터를 TodoList에 넣으려 한다면??
> 1. TodoList 컴포넌트에 데이터 생성 메서드 만들기
> 2. App에서 TodoList에 ref 달기
> 3. TodoInput에 ref가 달린 TodoList의 데이터 생성 메서드를 props로 전달하기
> 4. TodoInput의 state를 전달받은 함수에 파라미터로 넣기
>> ##### Remind : ref
>> ref는 DOM에 직접적인 접근을 해야할때 사용
>> - input / textarea 등에 포커스를 해야 할때
>> - 특정 DOM 의 크기를 가져와야 할 때
>> - 특정 DOM 에서 스크롤 위치를 가져오거나 설정을 해야 할 때
>> - 외부 라이브러리 (플레이어, 차트 등) 을 사용 할 때

### 10.3.1 텍스트 입력 관련(TodoInput) 상태 관리
> 1. App.js에 state선언 및 input value 정의
> 2. handleChange 함수 정의
> 3. TodoInput 컴포넌트에 props로 전달

- src/components/App.js
```javascript
import React, { Component } from "react";
import PageTemplate from "./PageTemplate";
import TodoInput from "./TodoInput";
import TodoList from "./TodoList";
 
class App extends Component {
  state = {
    input : "",
  };
  
  handleChange = (e) => {
    /* 
     * 변수 선언을 하려면 변수명을 value로 지정해야 e.target.value와 매핑됨.
     * 변수 선언 없이 e.target.value로 넣어도 무방함.
     */
    const {value} = e.target;
    // const {testValue} = e.target.value;
    this.setState({
      input : value
    });
  };
  
  render(){
    const {input} = this.state;
    const {handleChange} = this;
    return (
      <PageTemplate>
        <TodoInput onChange={handleChange} value={input}/>
        <TodoList/>
      </PageTemplate>
    );
  }
}
export defalut App;
```

### 10.3.2 초기 일정 데이터 정의 및 렌더링
> 1. todos 객체 배열 선언 (id, text, done으로 구성)
> 2. id : Todo의 고유 ID(key)
> 3. text : Todo의 내용
> 4. done : Todo의 체크 여부

- src/components/App.js
```javascript
import React, { Component } from "react";
import PageTemplate from "./PageTemplate";
import TodoInput from "./TodoInput";
import TodoList from "./TodoList";
 
class App extends Component {
  state = {
    input : "",
    todos : [
      { id:0, text: "리액트 공부하기", done:true},
      { id:1, text: "컴포넌트 스타일링 해보기", done:false}
    ]
  };
  
  handleChange = (e) => {
    /* 
     * 변수 선언을 하려면 변수명을 value로 지정해야 e.target.value와 매핑됨.
     * 변수 선언 없이 e.target.value로 넣어도 무방함.
     */
    const {value} = e.target;
    // const {testValue} = e.target.value;
    this.setState({
      input : value
    });
  };
  
  render(){
    const {input, todos} = this.state;
    const {handleChange} = this;
    return (
      <PageTemplate>
        <TodoInput onChange={handleChange} value={input}/>
        <TodoList todos={todos}/>
      </PageTemplate>
    );
  }
}
export defalut App;
```
- src/components/TodoList/TodoList.js
```javascript
import React, { Component } from "react";
import TodoItem from "../TodoItem";

class TodoList extends Component {
  render(){
    const {todos} = this.props;
    const todoList = todos.map (
      todo => (
        <TodoItem
          key={todo.id}
          done={todo.done}>
          {todo.text}
        </TodoItem>
      )
    );
    
    return (
      <div>
        {todoList}
      </div>
    )
  }
}
export default TodoList;
```

### 10.3.3 데이터 추가
> 일정추가하는 기능을 구현
> 1. handleInsert 함수 정의
> 2. id값 증가시켜주는 함수 정의

- src/components/App.js
```javascript
import React, { Component } from "react";
import PageTemplate from "./PageTemplate";
import TodoInput from "./TodoInput";
import TodoList from "./TodoList";
 
class App extends Component {
  state = {
    input : "",
    todos : [
      { id:0, text: "리액트 공부하기", done:true},
      { id:1, text: "컴포넌트 스타일링 해보기", done:false}
    ]
  };
  
  // 일정 데이터 안에 들어가는 id 값
  id = 1;
  getId = () => {
   return ++this.id; 
  }
  
  handleChange = (e) => {
    /* 
     * 변수 선언을 하려면 변수명을 value로 지정해야 e.target.value와 매핑됨.
     * 변수 선언 없이 e.target.value로 넣어도 무방함.
     */
    const {value} = e.target;
    // const {testValue} = e.target.value;
    this.setState({
      input : value
    });
  };
  
  handleInsert = () => {
    const {todos, input} = this.state;
    
    const newTodo = {
      text: input,
      done: false,
      id: this.getId()
    };
    
    this.setState({
      todos: [...todos, newTodo],
      input: ""
    })
  }
  
  render(){
    const {input, todos} = this.state;
    const {handleChange, handleInsert} = this;
    return (
      <PageTemplate>
        <TodoInput onChange={handleChange} onInsert={handleInsert} value={input}/>
        <TodoList todos={todos}/>
      </PageTemplate>
    );
  }
}
export defalut App;
```

### 10.3.4 데이터 수정
> 체크박스 클릭 시 수정이 이루어짐.
>
> id값으로 해당 데이터를 찾아 새로운 배열을 만들어 업데이트함.
> 1. handleToggle 함수 정의

- src/components/App.js
```javascript
import React, { Component } from "react";
import PageTemplate from "./PageTemplate";
import TodoInput from "./TodoInput";
import TodoList from "./TodoList";
 
class App extends Component {
  (...)
  
  handleToggle = (id) => {
    const {todos} = this.state;
    const index = todos.findIndex(todo => todo.id === id);
    
    /* 찾은 데이터의 done값(체크유무)을 반전 시킴 */
    const toggled = {
      ...todos[index],
      done : !todos[index].done
    };

    this.setState({
      todos : [
        ...todos.slice(0, index),
        toggled,
        ...todos.slice(index+1, todos.length)
      ]
    });
  }
  
  render(){
    const {input, todos} = this.state;
    const {handleChange, handleInsert, handleToggle} = this;
    return (
      <PageTemplate>
        <TodoInput onChange={handleChange} onInsert={handleInsert} value={input}/>
        <TodoList todos={todos} onToggle={handleToggle}/>
      </PageTemplate>
    );
  }
}
export defalut App;
```
- src/components/TodoList/TodoList.js
```javascript
import React, { Component } from "react";
import TodoItem from "../TodoItem";

class TodoList extends Component {
  render(){
    const {todos, onToggle} = this.props;
    const todoList = todos.map (
      todo => (
        <TodoItem
          key={todo.id}
          done={todo.done}
          onToggle={() => onToggle(todo.id)}>
          {todo.text}
        </TodoItem>
      )
    );
    
    return (
      <div>
        {todoList}
      </div>
    )
  }
}
export default TodoList;
```

### 10.3.5 데이터 제거
> [지우기] 버튼 클릭 시 수행 됨.
> 1. handleRemove 함수 정의 (해당 id값 찾아 제거)

- src/components/App.js
```javascript
import React, { Component } from "react";
import PageTemplate from "./PageTemplate";
import TodoInput from "./TodoInput";
import TodoList from "./TodoList";
 
class App extends Component {
  (...)
  
  handleRemove = (id) => {
    const {todos} = this.state;
    const index = todos.findIndex(todo => todo.id === id);
    
    this.setState({
      todos : [
        ...todos.slice(0, index),
        ...todos.slice(index+1, todos.length)
      ]
    });
  }
  
  render(){
    const {input, todos} = this.state;
    const {handleChange, handleInsert, handleToggle, handleRemove} = this;
    return (
      <PageTemplate>
        <TodoInput onChange={handleChange} onInsert={handleInsert} value={input}/>
        <TodoList todos={todos} onToggle={handleToggle} onRemove={handleRemove}/>
      </PageTemplate>
    );
  }
}
export defalut App;
```

- src/components/TodoList/TodoList.js
```javascript
import React, { Component } from "react";
import TodoItem from "../TodoItem";

class TodoList extends Component {
  render(){
    const {todos, onToggle, onRemove} = this.props;
    const todoList = todos.map (
      todo => (
        <TodoItem
          key={todo.id}
          done={todo.done}
          onToggle={() => onToggle(todo.id)}
          onRemove={() => onRemove(todo.id)}>
          {todo.text}
        </TodoItem>
      )
    );
    
    return (
      <div>
        {todoList}
      </div>
    )
  }
}
export default TodoList;
```
> onRemove => onToggle 순으로 함수가 실행되기에 setState가 동시에 일어나면서 업데이트 내용을 병합하기에 지우기 기능이 정상 작동 하지 않음.
>
> 그렇기에 부모로 이벤트 전파를 막기 위해 e.stopPropagation() 함수를 호출함. 
>
> `참고 https://programmingsummaries.tistory.com/313` 

- src/components/TodoItem/TodoItem.js
```javascript
import React,  { Component } from "react";
import styles from "./TodoItem.scss";
import classNames from "classnames/bind";
 
const cx = classNames.bind(styles);
 
class TodoItem extends Component {
  render(){
    const {done, children, onToggle, onRemove} = this.props;
 
    return (
      <div className={cx("todo-item")} onClick={onToggle}>
        <input className={cx("tick")} type="checkbox" checked={done} readOnly/>
        <div className={cx("text", { done })}>{children}</div>
        <div className={cx("delete")} onClick={(e) => {
        	onRemove();
        	e.stopPropagation();
        }}>[지우기]</div>
      </div>
    )
  }
}
export default TodoItem;
```
