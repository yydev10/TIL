# React
- UI인터페이스를 만들기 위한 자바스크립트 라이브러리

### 특징
- virtual dom을 이용해서 업데이트 할 부분을 최소한으로 업데이트 해서 화면을 보여줄 수 있음
- component 기반으로 의존성이 낮고, 독립적으로 작동해 기능 변경 시 다른 컴포넌트를 수정할 필요가 없음
- component는 재사용 가능하여 개발기간을 단축할 수 있고, 유지보수 용이

### 구조
:file_folder: Project
- package.json : 프로젝트 정보, npm모듈에 대한 정보를 담고있는 파일
- :file_folder: node_modules : npm모듈이 저장되는 위치
- :file_folder: src
    - index.js 
        - npm start 시 파일에 정의한 컴포넌트가 렌더링 되는 파일 
        - 파일 내부에 ```ReactDOM.createRoot(document.getElementById('root'))```가 index.html 내부의 <div> 태그 밑에 react dom을 생성함
        - 파일 내부에 정의한 ```root.render``` 함수에 의해 사용자 정의된 컴포넌트가 랜더링 됨
    - App.js 
    - ...

### JSX
- XML, HTML 코드를 내부적으로 자바스크립트로 변환한 것
- 코드가 간결해지고 가독성이 향상된다는 장점이 있음
- react dom은 랜더링 전에 임베딩한 값을 모두 문자열로 변환하기 때문에 명시적으로 선언되지 않은 값은 들어올 수 없으므로 injection attacks을 방어할 수 있음
- React.createElement 함수를 통해 JSX 코드를 자바스크립트로 변환, element를 생성해 리턴함
```javascript
React.createElement(
    type, //element의 유형
    [props], // 속성
    [...children] // 현재 elements의 자식 elements
)
```
