# 8장 함수형 컴포넌트


## 8.1 함수형 컴포넌트 사용법
### 클래스형
```javascript
import React, { Component } from 'react';

class Hello extends Component {
  render() {
    return (
      <div>Hello {this.props.name}</div>
    );
  }
}
```

### 함수형
```javascript
import React from 'react';

function Hello(props) {
  return (
    <div>Hello {props.name}</div>
  )
}
```

es6의 arrow function과 destructuring assignment를 이용하여 단순히 선언할 수 있습니다.

```javascript
import React from 'react';

const Hello = ({name}) => {
  return (
    <div>Hello {name}</div>
  )
}
```

## 8.2 언제 함수형 컴포넌트를 사용해 할까?
리액스v16이상에서 함수형 컴포넌트가 조금 빠르다.

라이프사이클, state가 필요할땐 클래스형
나머지는 모두 함수형
