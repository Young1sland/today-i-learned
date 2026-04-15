# javascript

## Import 방식 CommonJS VS ES module

### 1. CommonJS

Node.js에서 오래전부터 쓰던 방식

```js
내보내기
module.exports = function hello() {
  console.log('hi');
};

가져오기
const hello = require('./hello');
hello();
```

- require()가 module.exports에 들어있는 값을 그대로 가져옴

### 2. ES Module

자바스크립트의 표준 모듈 방식

```js
내보내기
export default function hello() {
  console.log('hi');
}

가져오기
import hello from './hello';
hello();
```

- default로 내보낸 값을 import xxx from으로 가져옴
