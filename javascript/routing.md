## 👉 Routing이란?

리액트를 써본 사람들은 `react-router-dom`을 쓰면서 라우팅 기능이 익숙할 수 있다.
라우팅은 출발지-목적지간의 경로를 결정하는 기능인데, 웹페이지에서 화면 전환을 위해 사용되는 기능쓰
라우팅 하는 방법은 여러 가지가 있지만, 그 중에서도 `hash routing` 방식을 이용해보았다!

## 👉 초기 Setting

### 🌟 index.html 파일 생성

`html:5`을 vscode html파일에 입력하면 짠!!
자동으로 html 템플릿을 만들어준다. 이걸로 `index.html`파일 만들긔

### 🌟 root div 생성

앞서 만든 `index.html`의 body태그 안에 `<div id="app"></div>`를 심어준다
리액트에서도 `index.html`파일에 root id를 가진 div태그가 있는 것처럼
우리도 root div를 하나 만들어준다!

(웹팩으로 설정을 했는데, 아직 저는 웹팩 마스터가 아니고 친절한 개발자분들의 블로그에서 야금야금 퍼온 정보로 야매로 세팅을 해서 웹팩 설정은 건너뛰겠읍니다 😂 )

## 👉 Layout이 되는 페이지 만들기

`src/pages` 폴더 아래에 `main.html`과 `mypage.html` 파일을 만들어주었다!

```
//main.html
  <a href="/">메임메인니닌</a>
  <a href="/my">my</a>
  <a href="https://naver.com">naver</a>
  메인 #{message}

```

리액트에서도 html파일은 index.html 하나이고, 나머지는 jsx 문법이 쓰이는 것처럼
main과 mypage는 html 양식을 온전히 쓰는 것이 아닌 태그와 message 변수만 적어두었다!

handlebars.js 에선 `{{ message }}` 이런식으로 중괄호를 두개 써서 표기하는데
나는 변수에 대해 `#{}` <- 이런식으로 표기하기로 했다!

# 🐟 🐈 🐈

### ** 여기 아래서부터 조금 많이 어려워짐주의!**

미리 준비하라는 의미에서 귀여운 고앵이티콘 준비쓰!

## 👉 router.js

### 컴포넌트 함수 만들기

```
const Component = template => (
  props => (
    Object.entries(props).reduce((acc, [key, value]) => {
      const keyword = new RegExp(`#{${key}}`, 'g');
      return acc.replace(keyword, value);
    }, template)
  )
);

const template =`눈누 #{message}`
console.log(Component(template)({ message : '난나'}))
// 결과 값은 '눈누 난나'

```

컴포넌트는 template을 인자로 받는 함수이고, 그 안에서 또 props라는 인자를 받는 함수를 리턴한다.
위의 콘솔을 통해 어떤 식으로 코드가 돌아가는지 확인했으면 본론으로 들어가보자!

일단 routeList라는 변수를 설정해 route의 path와 template이 포함된 배열을 만들어줄거다.

```js
import main from './pages/main.html';
import mypage from './pages/mypage.html';

const Component = (template) => (props) =>
  Object.entries(props).reduce((acc, [key, value]) => {
    const keyword = new RegExp(`#{${key}}`, 'g');
    return acc.replace(keyword, value);
  }, template);

const mainComponent = Component(main);
const mypageComponent = Component(mypage);

const routeList = [
  {
    path: '/',
    template: mainComponent,
  },
  {
    path: '/my',
    template: mypageComponent,
  },
];
```

### handleRouter 함수 만들기

```js
const handleRoute = (root, routes) => {
  const browserRoute = (() => {
    console.log(window.location.hash, '해시있어?');
    if (!window.location.hash) return '/';
    return window.location.hash.replace(/^#/, '');
  })();

  const currentRoute = routes.find((e) => e.path === browserRoute);
  root.innerHTML = currentRoute.template({
    message: '히히히히히ㅣㅣ',
  });

  const links = document.querySelectorAll('a');
  links.forEach((link) => {
    if (link.href.indexOf(window.location.origin) !== 0) return;
    link.addEventListener('click', (ev) => {
      ev.preventDefault();
      // 내가 임의로 링크 생성
      const targetURL = link.href.replace(window.location.origin, '#');
      window.history.pushState({}, 'dddd', targetURL);
      handleRoute(root, routes);
      const event = new CustomEvent('pagemount', {});
      document.dispatchEvent(event);
    });
    console.log(link.href);
  });
};
```

함수가 복잡하니 하나씩 살펴보자!

#### browserRoute

- 브라우저상의 라우트를 routeList와 맞추기 위해 만든 로직
- `console.log(window.location.hash)`를 찍어보면 경로가 나온다
- mypage의 경우 hash router를 통해 `/#/my` 라는 값이 찍힌다
- 여기서 `#/` 이 부분을 없애기 위해 정규표현식을 써준다
- 참고로 ^는 문자열의 맨 처음에 ^ 다음에 쓰여진 것을 매칭할 때 쓴다.
- 이리하여 `localhost:8080/#/my`에서 해시를 빼 `/my`라는 값만 남겨주게 된다.
- `if (!window.location.hash) return '/';` 이 조건을 안 넣으면 메인페이지에서부터 에러~

#### currentRoute

- 위에서 맞춰준 browserRoute와 currentRoute가 동일할 때 root div에 적용
- 위에서 만들어준 커링의 마지막단계 실행이 여기서 이루어진다!
- 인자로 받는 객체를 넣어주면 완성!

#### link

- localhost가 아닌 외부 링크가 걸려있는 경우 처리하기 위함
- `if (link.href.indexOf(window.location.origin) !== 0) return;`
- origin이 `"http://localhost:8080"`인 경우만 남기기 위해 indexOf 메소드 이용
- pushState 메소드는 세 개의 인자를 받는데, 차례로 state, title, url이다.
- 위에서 정의한 targetURL을 적용하고, handleRoute 함수를 실행한다
- 여기서 handleRoute를 실행하지 않으면 routing시 주소값만 바뀌고 제대로 이동이 되지 않는다!
- 마지막 두 줄은 왜 넣어야 하는지 모르겠어서 수정 예정...!! @#$@$@#$%@#%#$%#$%@#$!@#@$#$%^#$%@#$@

#### initApp

```js
const initApp = (el, routes) => {
  const root = document.querySelector(el);
  document.addEventListener('readystatechange', (ev) => {
    // 최초 브라우저 실행시 or 새로고침시
    if (ev.target.readyState === 'complete') {
      handleRoute(root, routes);
    }
  });
  // 뒤로가기
  window.addEventListener('popstate', () => {
    handleRoute(root, routes);
  });
};

initApp('#app', routeList);
```

- 위에서 정의한 handleRoute 함수가 실제로 적용되는 부분
- readyState를 통해 최초 브라우저 실행 혹은 새로고침 때 로직이 적용되게 한다!
- popstate를 통해 뒤로가기 로직이 적용되게 한다!

아... 너무 어려워.......
