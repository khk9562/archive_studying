---
title: "[모듈 번들러] Webpack과 Vite"
tags: ["vite", "webpack"]
date: 2024-06-01
velog_id: 9a20b135-8093-48c1-aae1-c8a529eb2789
velog_url: https://velog.io/@steela/모듈-번들러-Webpack과-Vite
velog_updated: 2026-07-21T15:53:52.386Z
synced_at: 2026-07-22
---

> 🔗 원본: [velog.io/@steela/모듈-번들러-Webpack과-Vite](https://velog.io/@steela/모듈-번들러-Webpack과-Vite) · 📅 2024-06-01
# 모듈 번들러란?
개발을 하다보면 파일 하나로만 원하는 기능을 구현 및 관리할 수 없고, 어느 시점부터 반드시 파일을 여러개로 분리해야하는 시점이 온다. 이 분리된 파일 하나하나를 **모듈**이라 칭한다.
개발 과정에서 반드시 중복 및 재사용되는 코드가 있기 마련이고, 그걸 모듈화 해놓고 필요시 찾아 쓰는 것이다.

그리고 **모듈 번들러**란, 이렇게 필요에 의해 분리된 코드를 하나의 파일로 병합하는 개발도구이다.
분리해놓은 것을 왜 다시 하나로 병합하는가?



## 모듈 번들러 사용 이유

단순하게 HTML과 Javascript로 프론트 개발을 하면, HTML 파일에서 Javascript 원본 소스를 제공하여 브라우저에서 이 스크립트를 순서대로 로드하는 방식인 아주 간단한 모듈 시스템만이 제공된다.

```
<html>
	<script src="/src/a.js"></script>
    <script src="/src/b.js"></script>
    <script src="/src/c.js"></script>
</html>
```

하지만 브라우저 내에서 자바스크립트는 파일이 여러개여도 하나의 파일 안에 있는 것처럼 전역(window)를 공유한다. **즉, 스크립트를 로드한 전역 컨텍스트에서 각 모듈 간의 충돌이 발생할 수 있다.**
그래서 다른 모듈과 변수 이름이 겹치지 않도록 모듈 로드 순서를 지정하는데 많은 노고가 필요하다.
하지만 세상이 발전하며 웹 개발 과정은 점차 단순한 문서 읽기에 그치지 않고 매우매우매우 복잡해진다.
그 과정에서 모듈화 및 모듈 호출 방식 등을 돕는 모듈 번들러가 필요에 의해 등장하게 됩니다.
(관련 역사는 이 [사이트](https://yozm.wishket.com/magazine/detail/1261/) 참고를 권장드립니다!)

그리고 그 많고 많은 모듈 번들러 중에서도, 가장 많이 사용되는 webpack과 내가 앞으로 사용하게 될 수도 있는 vite에 대해 이야기 하겠다.


# Webpack

![](https://velog.velcdn.com/images/steela/post/6d2e4dcc-4564-452f-b19b-5ac549d02ebf/image.png)


프론트엔드 개발에서 가장 오래되고 알려져 안정성이 뛰어나 가장 많이 사용되는 모듈 번들러이다.
웹팩은 하나의 시작점(Entry point)으로부터 의존적인 모듈을 전부 찾아내서 하나의 파일로 만든다.

```
// webpack.config.js

// Single Page Application(SPA)
module.exports = {
  entry: './src/index.js'
}

// Multi Page Application (MPA)
module.exports = {
  entry: {
    login: './src/LoginView.js',
    main: './src/MainView.js'
  }
}
```
이때 최초 진입점이 되는 대상 파일은 웹 애플리케이션의 전반적인 구조와 내용이 담겨있어여 한다.
그래야 웹팩이 해당 파일을 토대로 애플리케이션의 모듈들의 연관관계에 대해 이해하고 분석하고 합치기 때문이다.

<br />

### Output
웹팩을 실행하여 빌드하고 난 후 결과물의 파일 경로를 의미한다.
**filename** 웹팩으로 빌드한 파일의 이름을 의미하며,
**path**는 해당 파일의 경로를 의미한다.

```
// webpack.config.js
var path = require('path');

module.exports = {
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist')
  }
}

/* Node.js API가 하는 역할은 아래 코드와 동일하다. */
output: './dist/bundle.js'
```

### Loader
웹팩이 애플리케이션을 해석할때 자바스크립트 파일이 아닌 HTML, CSS, Images, font 등을 변환할 수 있게 도와주는 속성이다. 로더로 설정을 지정해주지 않으면 웹팩이 해당 파일을 읽을 수 없어 에러가 발생한다.

```
// webpack.config.js

module.exports = {
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' },
      // ...
    ]
  }
}
```

### Plugin
웹팩의 기본적인 동작에 추가적인 기능을 제공하는 속성이다.

```
// webpack.config.js
var webpack = require('webpack');
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  plugins: [
    new HtmlWebpackPlugin(),
    new webpack.ProgressPlugin()
  ]
}

```

HtmlWebpackPlugin : 웹팩으로 빌드한 결과물로 HTML 파일을 생성해주는 플러그인
ProgressPlugin : 웹팩의 빌드 진행율을 표시해주는 플러그인



# Vite
애플리케이션의 발전에 따라 대규모 프로젝트에서는 Javascript 모듈의 개수가 수천 개 이상에 도달하며, 속도 저하 즉 성능 저하 문제가 발견되었다. 기존 webpack으로도 충분히 커버되던 상황이 그렇지 않아지며 더 나은 성능의 모듈 번들러를 필요로 하게 되면서 Vite가 등장하였다.

**vite는 애플리케이션의 모듈을 dependencies와 source code 두 가지 카테고리로 나누어 개발 서버의 시작 시간을 개선한다.**

### Dependencies
개발 시 그 내용이 바뀌지 않을 일반적인(Plain) JavaScript 소스 코드이다.

Vite의 사전 번들링 기능은 Esbuild를 사용하며,
Go로 작성된 Esbuild는 Webpack, Parcel과 같은 기존의 번들러 대비 10-100배 빠른 속도를 제공한다.

### Source Code
Vite는 Native ESM을 이용해 소스 코드를 제공한다. 이것은 본질적으로 브라우저가 번들러의 작업의 일부를 차지할 수 있도록 하여 Vite는 브라우저가 요청하는 대로 소스 코드를 변환하고 제공하기만 하면 된다. 조건부 동적 import 이후의 코드는 현재 화면에서 실제로 사용되는 경우에만 처리된다.


### 소스 코드 갱신 속도 향상

기존 번들러 기반으로 개발 진행 시, 소스 코드 업데이트가 이뤄지면 번들링 과정을 다시 거쳐야 한다. 따라서 서비스가 커질수록 소스 코드 갱신 시간 또한 선형적으로 증가하며 성능 저하를 불러일으킨다.

이 이슈에 대한 대안으로 HMR(Hot Module Replacement)가 등장하였지만, vite 측은 이것이 이슈에 대한 명확한 해답이 아니라고 느꼈다고 한다. vite 또한 HMR을 제공하지만 이는 번들러가 아닌 ESM을 이용한다.

Vite는 어떤 모듈이 수정되면 그저 수정된 모듈과 관련된 부분만을 교체할 뿐이고, 브라우저에서 해당 모듈을 요청하면 교체된 모듈을 전달할 뿐이다. 전 과정에서 완벽하게 ESM을 이용하기에, 앱 사이즈가 커져도 HMR을 포함한 갱신 시간에는 영향을 끼치지 않는다.

또한 vite는 HTTP 헤더를 활용하여 전체 페이지의 로드 속도를 높인다. 필요에 따라 소스 코드는 304 Not Modified로, 디펜던시는 Cache-Control: max-age=31536000,immutable을 이용해 캐시된다. 이렇게 함으로써 요청 횟수를 최소화하여 페이지 로딩을 빠르게 만들어 준다.


## 결론
Vite는 개발 서버 시작, HMR 및 프로덕션 빌드에서 속도가 뛰어나 현대 웹 기술에 중점을 둔 성능 중심 프로젝트에 이상적이다.
반면, Webpack은 안정성, 플러그인 생태계 등에서 강점을 보인다고 할 수 있겠다.

<br />


> ### 참고
https://velog.io/@ssulv3030/%EB%AA%A8%EB%93%88-%EB%B2%88%EB%93%A4%EB%9F%AC%EB%9E%80webpack-pacel-rollup-%EB%B9%84%EA%B5%90
https://bribrie.tistory.com/84
https://yozm.wishket.com/magazine/detail/1261/
https://ingg.dev/webpack/
https://velog.io/@gusdh2/Webpack%EC%9D%B4%EB%9E%80-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%A0%EA%B9%8C%EC%9A%94
https://ko.vitejs.dev/guide/why
https://medium.com/@pradeeptiwari.bhumca10/a-comprehensive-comparison-vite-vs-webpack-8e1e727ee027
