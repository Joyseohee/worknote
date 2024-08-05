# ESM과 CommonJS

ESM (ES Modules)과 CommonJS는 JavaScript에서 모듈을 정의하고 사용하는 두 가지 주요 방법입니다. 각각의 모듈 시스템은 고유한 특성과 사용 방식을 가지고 있으며, 아래에서 두 모듈 시스템의 특성을 비교하고 설명합니다.

### ES Modules (ESM)

1. **구문(Syntax)**:
   - ESM은 `import`와 `export` 키워드를 사용합니다.
   ```javascript
   // module.mjs
   export const myVariable = 42;
   export function myFunction() {
     console.log('Hello from ESM!');
   }
   
   // main.mjs
   import { myVariable, myFunction } from './module.mjs';
   console.log(myVariable); // 42
   myFunction(); // Hello from ESM!
   ```

2. **정적 로딩(Static Loading)**:
   - ESM은 컴파일 시점에서 모듈을 정적으로 분석합니다. 이를 통해 트리 쉐이킹(tree shaking)과 같은 최적화를 수행할 수 있습니다.
   - 모든 `import` 구문은 파일 최상단에 위치해야 하며, 런타임에서 동적으로 결정할 수 없습니다.

3. **엄격한 모드(Strict Mode)**:
   - ESM은 자동으로 strict mode가 적용됩니다. 이를 통해 코드의 안전성과 일관성을 높입니다.

4. **네임스페이스 격리(Namespace Isolation)**:
   - 각 모듈은 고유한 스코프를 가지며, 전역 네임스페이스 오염을 방지합니다.

5. **비동기 로딩(Asynchronous Loading)**:
   - ESM은 비동기적으로 로드됩니다. 이는 특히 브라우저 환경에서 네트워크 요청의 비동기 특성과 잘 맞아떨어집니다.

6. **브라우저 지원**:
   - 최신 브라우저들은 기본적으로 ESM을 지원합니다. HTML에서 `<script type="module">`을 사용하여 ESM을 직접 로드할 수 있습니다.
   ```html
   <script type="module">
     import { myFunction } from './module.mjs';
     myFunction();
   </script>
   ```

### CommonJS

1. **구문(Syntax)**:
   - CommonJS는 `require`와 `module.exports`를 사용합니다.
   ```javascript
   // module.js
   const myVariable = 42;
   function myFunction() {
     console.log('Hello from CommonJS!');
   }
   module.exports = { myVariable, myFunction };
   
   // main.js
   const { myVariable, myFunction } = require('./module.js');
   console.log(myVariable); // 42
   myFunction(); // Hello from CommonJS!
   ```

2. **동적 로딩(Dynamic Loading)**:
   - CommonJS 모듈은 런타임에 동적으로 로드됩니다. `require` 구문은 코드의 어디에서나 사용할 수 있습니다.
   - 이를 통해 조건부 로딩이나 런타임에 모듈 경로를 결정할 수 있습니다.

3. **모듈 캐싱(Module Caching)**:
   - CommonJS 모듈은 한 번 로드되면 캐시되며, 이후의 `require` 호출은 캐시된 모듈을 반환합니다. 이는 성능 향상에 기여합니다.

4. **동기적 로딩(Synchronous Loading)**:
   - CommonJS는 동기적으로 모듈을 로드합니다. 이는 서버 사이드 환경(Node.js)에서는 문제가 없지만, 브라우저 환경에서는 성능에 영향을 미칠 수 있습니다.

5. **네임스페이스 격리(Namespace Isolation)**:
   - CommonJS 모듈도 고유한 스코프를 가지며, 전역 네임스페이스 오염을 방지합니다.

6. **서버 사이드(Node.js) 지원**:
   - CommonJS는 주로 Node.js 환경에서 사용됩니다. Node.js는 기본적으로 CommonJS를 지원합니다.

### 비교

| 특성                | ES Modules (ESM)                   | CommonJS                         |
|---------------------|------------------------------------|----------------------------------|
| 구문(Syntax)        | `import`, `export`                 | `require`, `module.exports`      |
| 로딩 방식           | 정적 로딩(Static)                  | 동적 로딩(Dynamic)               |
| 모드                | 자동 strict mode                   | 명시적 strict mode 사용 가능     |
| 네임스페이스 격리   | 네임스페이스 격리                 | 네임스페이스 격리               |
| 로딩 특성           | 비동기 로딩(Asynchronous)          | 동기 로딩(Synchronous)          |
| 지원 환경           | 최신 브라우저 및 Node.js           | 주로 Node.js                     |
| 트리 쉐이킹         | 지원                               | 제한적 지원                      |

### 결론

- **ESM**: 최신 브라우저와 Node.js 환경에서 사용되는 표준 모듈 시스템으로, 정적 분석과 트리 쉐이킹을 통해 최적화가 용이합니다. 주로 클라이언트 사이드와 최신 JavaScript 코드베이스에서 사용됩니다.
- **CommonJS**: Node.js 환경에서 주로 사용되는 모듈 시스템으로, 동적 로딩과 동기적 특성을 가집니다. 서버 사이드 개발에 적합합니다.

각 모듈 시스템은 고유한 장점과 단점을 가지며, 사용하는 환경과 요구 사항에 따라 선택됩니다.