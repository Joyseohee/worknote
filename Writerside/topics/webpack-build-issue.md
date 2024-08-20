# 웹팩 빌드로 인한 라이브러리 내 함수 누락 이슈

웹팩 빌드 시 다양한 형식의 파일(JS, CSS, 이미지 등)을 모듈로 처리하여 브라우저에서 효율적으로 로드할 수 있게 하나 혹은 여러 개의 번들 파일로 결합합니다.
이 때 각 모듈의 의존성을 분석해서 최적화를 수행합니다.
이번 이슈는 이 과정에서 모듈 간의 의존성이 제대로 파악되지 않아 발생한 것으로 추정됩니다.
모듈 간의 의존성이 제대로 파악되지 않아 번들 파일에서 `dayjs` 라이브러리 내 함수 `this.init()`이 누락되었습니다. 

## 트리 쉐이킹(Tree Shaking)
웹팩 빌드시 번들링 과정에서 트리 쉐이킹 기법이 사용됩니다.
트리 쉐이킹은 Webpack과 같은 모듈 번들러에서 사용하지 않는 코드를 제거해서 최종 번들 파일의 크기를 줄이는 최적화 기법입니다.
주로 ESM에서 효과적으로 적용됩니다. 

> **이슈 원인**
>
> 트리 쉐이킹 과정에서 dayjs 모듈을 ESM이 아닌 *CommonJS*로 불러오면서 모듈 간 의존성을 제대로 파악하지 못해 함수가 잘못 삭제된 것으로 보입니다.
>
{style="warning"}

### 원리
프로그램의 의존성 그래프를 분석해서 어떤 코드를 실제로 사용하고 있는지 파악합니다. 사용하지 않는 코드는 번들에서 제외됩니다.

### 과정
<procedure title="트리 쉐이킹의 작동 과정" id="process_treeshaking">
    <step>
        <p>모듈 그래프 생성</p>
        <p>번들러는 애플리케이션의 엔트리 포인트부터 시작해서 모든 모듈의 의존성을 추적합니다.</p>
        <p>이를 통해 모듈 그래프를 생성합니다.</p>
    </step>
    <step>
        <p>사용되지 않는 코드 식별</p>
        <p>모듈 그래프에서 각 모듈의 코드 중 사용되지 않는 부분을 식별합니다.</p>
        <p>ESM에서는 'import'와 'export' 구문을 사용하여 모듈 간의 의존성을 명확히 알 수 있으므로 트리 쉐이킹이 더 효과적입니다.</p>
    </step>
    <step>
        <p>코드 제거</p>
        <p>사용되지 않는 코드(dead code)를 번들에서 제거합니다.</p>
        <p>이 과정은 번들 크기를 줄이고 로드 시간을 단축합니다.</p>
    </step>
</procedure>

### 조건
- ES 모듈 사용 : 트리 쉐이킹은 ESM에서 정적 분석이 가능하기 때문에 효과적입니다. CommonJS 모듈은 런타임에 로드되므로 트리 쉐이킹 적용이 어렵습니다.
- Side Effects 설정 : 패키지의 package.json 파일에서 'sideEffects' 필드를 설정하면 트리 쉐이킹 최적화를 도울 수 있습니다.

```json
{
  "name": "example-package",
  "version": "1.0.0",
  "main": "index.js",
  "module": "index.esm.js",
  "sideEffects": false
}
```

- Webpack 설정에서 트리 쉐이킹 활성화 : Webpack에서 트리 쉐이킹을 활성화하려면 mode를 production으로 설정하거나 optimization 옵션을 조정해야 합니다.

```Javascript
module.exports = {
  mode: 'production', // or 'development'
  optimization: {
    usedExports: true, // Ensure usedExports is enabled for tree shaking
  },
};
```

webpack 2 릴리스에서는 ES2015모듈과 사용하지 않는 모듈의 export를 감지하는 기능을 제공하고 
4 버전은 package.json의 "sideEffects" 프로퍼티를 통해 컴파일러에 힌트를 제공하는 방식으로 기능을 확장합니다.
프로젝트의 어떤 파일이 순수한지 나타내며 만약 사용하지 않는다면 제거해도 괜찮은지 표시합니다.

## CommonJS와 ESM
<procedure title="모듈 시스템" id="js_module_system">
    <p>JavaScript 코드의 모듈화를 이뤄내기 위한 모듈 시스템 방식으로는 <b>CommonJS</b>와 <b>ESM</b>방식이 있습니다.</p>
    <step>
        <p>CommonJS</p>
        <p>Node.js에서 널리 사용되는 모듈 시스템으로, require와 module.exports를 사용하여 모듈을 가져오고 내보냅니다. CommonJS 모듈은 런타임에 로드됩니다.</p>
        <p>wehago-ui 프로젝트에서는 webpack 설정 파일에서 이 같은 문법을 쉽게 찾아볼 수 있습니다.</p>
        <p>런타임 시점에 모듈을 동적으로 로드하므로 require 구문을 <b>코드 어디에서나 사용</b>할 수 있습니다.</p>
        <p>동적 로드 기능 덕분에 조건부 로딩이 가능하고 런타임에 모듈 경로를 결정할 수 있습니다.</p>
        <img src="webpack_config.png" alt="webpack_config" border-effect="line"/>
    </step>
    <step>
        <p>ESM(ECMAScript Modules)</p>
        <p>최신 JavaScript 표준으로, import와 export를 사용합니다. ESM 모듈은 정적으로 로드됩니다.</p>
        <p>wehago-ui 프로젝트에서는 각 컴포넌트에서 다른 컴포넌트나 라이브러리를 가져올 때 사용하고 있습니다.</p>
        <p>컴파일 시점에 모듈을 정적으로 분석하므로 import 구문은 <b>반드시 파일 최상단</b>에 위치해야 합니다.</p>
        <p>정적 로드 때문에 런타임에 동적으로 모듈 경로를 결정할 수 없는 대신 트리 쉐이킹 같은 최적화가 가능합니다.</p>
        <img src="esm_wehagoui.png" alt="esm_wehagoui" border-effect="line"/>
    </step>

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
</procedure>

### ESM 빌드로 init 함수가 사라지는 이슈가 해결 된 이유
1. 정적 분석과 트리 쉐이킹:

ESM 빌드는 정적 분석을 통해 모듈 간의 의존성을 명확히 파악할 수 있습니다. 이를 통해 Webpack은 불필요한 코드를 제거하는 트리 쉐이킹을 더 정확하게 수행할 수 있습니다.
반면, CommonJS는 동적 로딩을 사용하기 때문에, Webpack이 모듈 간의 의존성을 정확히 파악하기 어려워 트리 쉐이킹이 덜 효과적입니다.

2. ESM의 모듈 스코프:

ESM 모듈은 각각 고유한 스코프를 가지므로, 전역 변수나 함수 충돌이 발생하지 않습니다. 따라서 init 함수가 올바르게 포함되고 동작할 수 있습니다.
CommonJS 모듈은 런타임에 로드되므로, 의도하지 않게 코드가 제거되거나 충돌이 발생할 수 있습니다.

### CommonJS에서 init 함수가 사라진 이유
1. 트리 쉐이킹 부작용:

Webpack은 CommonJS 모듈에서도 트리 쉐이킹을 시도하지만, 동적 로딩 특성상 정확히 불필요한 코드를 제거하기 어렵습니다. 이 과정에서 init 함수가 의도치 않게 제거될 수 있습니다.

2. 번들 최적화 문제:

Webpack이 CommonJS 모듈을 번들링할 때, 모듈 간의 의존성을 완벽히 파악하지 못해 필요한 코드를 누락시킬 수 있습니다. 이로 인해 init 함수가 번들에서 사라질 수 있습니다.

### TEDGE-UI에 적용된 방식
로컬 테스트 결과 아래 두 가지 모두 정상적으로 동작했습니다.

1. `package.json`에 `sideEffects`를 추가해서 해당 파일에 부수 효과가 있음을 표시하고 트리 쉐이킹 단계에서 해당 파일을 제외하는 방식
2. `webpack.config.js`에 `alias`를 추가해서 직접 ems 모듈을 불러오는 방식


```json
{
  "name": "tedge",
  "version": "0.1.0-alpha.1",
  "description": "",
  "sideEffects": [
    "./node_modules/dayjs"
  ]
}
```
```Javascript
module.exports = {
    resolve: {
        extensions: [".js", "es6"],
        alias: {
            dayjs: path.resolve(PATHS.node_module, 'dayjs/esm/index.js'),  // ESM build 파일을 이용
        }
    },
}
```

<seealso>
    <category ref="wrs">
        <a href="https://webpack.kr/guides/tree-shaking/">트리 쉐이킹</a>
        <a href="https://webpack.kr/guides/tree-shaking/#mark-the-file-as-side-effect-free">Tree Shaking</a>
        <a href="https://ui.toast.com/weekly-pick/ko_20180716">트리 쉐이킹으로 자바스크립트 페이로드 줄이기</a>
        <a href="https://medium.com/@hong009319/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-%ED%91%9C%EC%A4%80-%EC%A0%95%EC%9D%98-commonjs-vs-es-modules-306e5f0a74b1">자바스크립트의 표준 정의 : CommonJS vs ES Modules</a>
        <a href="https://f-lab.kr/insight/commonjs-vs-esmodule-20240523">CommonJS와 ES Module의 차이점과 선택 기준</a>
        <a href="https://tech.kakao.com/posts/605">CommonJS에서 ESM으로 전환하기</a>
        <a href="https://github.com/webpack/webpack/issues/6065">Allow app authors to force libraries into sideEffects: false</a>
    </category>
</seealso>