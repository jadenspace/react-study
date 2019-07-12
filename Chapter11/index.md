# 11장 컴포넌트 리렌더링 최적화

: 목차

[11.1 문제점 찾기](#11.1-문제점-찾기) 

[11.2 최적화 진행](#11.2-최적화-진행) 

[11.3 정리](#11.3-정리)

----------
 
## 11.1 문제점 찾기

### 11.1.1 리액트 개발자 도구의 Highlight Updates
크롬 웹브라우저에서 리액트 개발자 도구를 연 후 Highlight Updates 옵션을 활성화. 
활성화하면 리렌더링이 될때마다 화면에 리렌더링 빈도에 따라 하늘색 -> 초록색 -> 노란색 -> 빨간색 순으로 표시가 됨.
리액트는 부모 컴포넌트가 리렌더링되면 자식 컴포넌트도 리렌더링 되는 속성이 있음. 즉, App 컴포넌트가 리렌더링 되면
그 하위 모든 컴포넌트 들도 리렌더링 됨.

여기서 논의하는 리렌더링은 웹브라우저 페이지에 나타난 DOM이 아닌 Virtual DOM에 하는 렌더링을 의미함. 
React가 어떤 DOM을 변경 할지 인지하려면 Virtual DOM에 모두 렌더링해 보아야하는데 이때 불필요한 렌더링을
shouldComponentUpdate로 방지함. 그러면 Virtual DOM에 렌더링하는 과정에서 render() 함수를 실행하지 않고
이전에 썼던 DOM의 정보를 그대로 사용하기에 자원을 아낄 수 있음.

### 11.1.2 데이터 준비
- src/components/App.js
```javascript
import React, { Component } from "react";
import PageTemplate from "./PageTemplate";
import TodoInput from "./TodoInput";
import TodoList from "./TodoList";

const initData = new Array(500).fill(0).map(
    (foo, index) => ({id:index, text:`일정 ${index}`, done:false})
);

class App extends Component {
    state = {
        input : "",
        todos : initData
    };

    (...)
}

export default App;
```
### 11.1.3 크롬 개발자 도구 [Performance] 탭 사용
크롬으로 페이지를 띄운 후 크롬 개발자 도구를 클릭하여 Performance 탭에서 확인 가능
> `해당 챕터는 책 참고` 

## 11.2 최적화 진행

###11.2.1 TodoList 컴포넌트 최적화
TodoList 컴포넌트는 todos props를 바꿀 때만 리렌더링 되어야 함. 
따라서 shouldComponentUpdate 메서드를 사용하여 todos props에 대한 조건 설정.
- src/components/TodoList/TodoList.js
```javascript
import React, { Component } from "react";
import TodoItem from "../TodoItem";

class TodoList extends Component {
    /* 리렌더링 시 불필요한 렌더링을 하지않기 위함... */
    shouldComponentUpdate(nextProps, nextState){
        return this.props.todos !== nextProps.todos;
    }

    (...)
}

export default TodoList;
```

### 11.2.2 TodoItem 컴포넌트 최적화 
TodoItem 컴포넌트는 done 값이 바뀔 때만 리렌더링 되어야 함.
따라서 shouldComponentUpdate 메서드를 사용하여 done 값에 대한 조건 설정.
```javascript
import React,  { Component } from "react";
import styles from "./TodoItem.scss";
import classNames from "classnames/bind";

const cx = classNames.bind(styles);

class TodoItem extends Component {
    /* 리렌더링 시 불필요한 렌더링을 하지않기 위함... */
    shouldComponentUpdate(nextProps, nextState){
        return this.props.done !== nextProps.done;
    }

    (...)
}

export default TodoItem;
```
## 11.3 정리
- 리렌더링 성능을 향상 시켜주어야하는 상황은 크게 3가지로 나눌 수 있음.
1.  컴포넌트 배열이 렌더링되는 리스트 컴포넌트일 때
2. 리스트 컴포넌트 내부에 있는 아이템 컴포넌트일 때
3. 하위 컴포넌트 개수가 많으며, 리렌더링되지 말아야 할 상황에서도 리렌더링이 진행될 때

