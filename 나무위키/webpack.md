[[분류:프로그래밍 언어]][[분류:스크립트 언어]]
||<-2><tablealign=right><bgcolor=#435866><tablewidth=350> {{{#ccc {{{+1 '''webpack'''}}}}}} ||
||<-2><bgcolor=#FFFFFF> {{{#!wiki style="margin: 15px auto;text-align:center"
[[파일:webpack-logo.png|width=300]]}}}||
||<bgcolor=#435866> {{{#ccc '''종류'''}}} ||모듈 번들러 ||
||<bgcolor=#435866> {{{#ccc '''라이선스'''}}} ||[[MIT 라이선스]] ||
||<bgcolor=#435866> {{{#ccc '''버전'''}}} ||5 ||
||<-2> [[https://nodejs.org/ko/|홈페이지]] [[https://nodejs.org/ko/|깃허브]] ||
[목차]
[clearfix]
== 개요 ==
[[자바스크립트]] 기반의 모듈[\* 여기서에 모듈은 [[자바스크립트]]의 모듈에만 국한되지 않고 웹 애플리케이션을 구성하는 모든 자원을 의미합니다. ] 번들러 이다. 웹 어플리케이션 개발에 필요한 다양한 요소(HTML, CSS, Javascript, Images, Font, ~~친구~~ 등...)들을 하나의 파일로(혹은 여러 개의 파일로) 병합 및 압축을 해주는 역할을 한다. 주요한 요소로는 Entry, Output, Loaders, Plugins, Mode, Browser Compatibility 가 있다.

== 사용해 보기 ==
=== 설치 ===
기본적으로 [[Node.js]]가 설치되어 있다면,[[npm]]을 통해서 다운 받을 수 있다.
{{{npm install --save-dev webpack
}}}
[[Node.js]]의 비공식 매니저인 yarn 으로도 설치할 수 있다.
{{{yarn add webpack --dev
}}}

=== 설정하기 ===
webpack.config.js 라는 이름의 파일로 webpack의 사용자 정의의 설정을 할 수 있다.
{{{#!syntax javascript
const path = require('path');

module.exports = {
entry: './src/index.js',
output: {
path: path.resolve(\_\_dirname, 'dist'),
filename: 'bundle.js',
},
};
}}}
다음과 같이 파일을 설정한 뒤에 터미널에서 실행시키면 bundle.js 라는 결과물이 나오게 된다.

[[Node.js]]로 구현된 서버와 클라이언트가 한 프로젝트 내부에 존재해서 양측의 설정이 별도로 필요하거나, 혹은 개발용 설정과 배포용 설정을 구분하고자 하는 등 다수의 설정 파일이 필요한 경우 아래와 같이 만들어 사용할 수 있다.

{{{webpack --config webpack.config.server.dev.js
webpack --config webpack.config.server.prod.js
webpack --config webpack.config.client.js
}}}
각각의 config.js 파일들은 별개의 [[JavaScript]] 모듈로 취급될 수 있으므로 공통된 부분을 하나의 config 파일(가령 webpack.config.base.js)로 분리하여 이를 다른 파일에서 require함으로써 코드 재사용성을 높일 수도 있다.

=== 설정 요소 ===
'''Entry'''
{{{#!syntax javascript
module.exports = {
  entry: './path/to/my/entry/file.js',
};
}}}
엔트리 포인트는 webpack이 빌드될 때 최초의 진입점이자, 의존성 그래프의 시작점이라고 생각하면 된다. 의존성을 생각할 때, 페이지가 여럿으로 나뉜다면 다음과 같이 여러 개의 엔트리 포인트를 설정할 수 있습니다.

{{{#!syntax javascript
entry: {
  APage: './src/AScript.js',
  BPage: './src/BScript.js',
  CPage: './src/CScript.js'
};
}}}

'''Output'''
{{{#!syntax javascript
const path = require('path');

module.exports = {
entry: './path/to/my/entry/file.js',
output: {
path: path.resolve(\_\_dirname, 'dist'),
filename: 'my-first-webpack.bundle.js',
},
};
}}}
아웃풋은 웹팩을 사용하고 나서 나오는 결과물에 대한 설정입니다. path는 결과물의 위치, filename 결과물의 파일 이름을 설정하는 요소입니다. 지금 설정으로 폴더 이름은 '''dist''', 번들 파일 이름은 '''my-first-webpack.bundle.js'''이다. 이 번들 파일을 script 로 연결시키면 된다.

그 외의 Output 의 결과 파일의 charset, 파일이름 템플릿 설정, chunk 설정 요소는 공식문서를 참조해주세요. [[https://webpack.js.org/configuration/output/|공식문서]]

'''Loaders'''
{{{#!syntax javascript
const path = require('path');

module.exports = {
output: {
filename: 'my-first-webpack.bundle.js',
},
module: {
rules: [{ test: /\.txt$/, use: 'raw-loader' }],
},
};
}}}
로더는 자바스크립트 파일이 아닌 애플리케이션의 요소들을 번들링할 때, 이것들을 변환하는데 있어서 적용할 일종의 변환 규칙들입니다. 여러 타입의 파일을 처리하고, 애플리케이션에서 사용하게 변환하고, 의존성 그래프에 추가합니다. 예시에서 '''test''' 요소는 규칙이 적용될 파일을 가르키는데 예시에서는 '''.txt''' 파일에 적용되는 것 입니다. 그 적용되는 규칙은 '''raw-loader'''입니다.

로더를 불러오는 방법으로는 3 가지가 있습니다.

- 설정파일 (추천하는 방법)
- 인라인
- CLI

'''Plugins'''
{{{#!syntax javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); //installed via npm
const webpack = require('webpack'); //to access built-in plugins

module.exports = {
module: {
rules: [{ test: /\.txt$/, use: 'raw-loader' }],
},
plugins: [new HtmlWebpackPlugin({ template: './src/index.html' })],
};
}}}
플로그인은 webpack을 위한 확장 기능입니다. 넓게 보면 번들 최적화, 에셋 관리, 환경요소 주입 등의 일들에 있어서 webpack 도움을 줍니다.

플로그인를 사용하는 방법으로는 2 가지가 있습니다.

- 설정파일 (추천하는 방법)
- Node API

'''Mode'''
{{{#!syntax javascript
module.exports = {
  mode: 'production',
};
}}}
모드는 빌드때에 맞는 상황에 맞추어서 최적화 요소를 제공해주는 webpack 4 버전에 생긴 기능입니다. 디폴트는 '''production'''입니다.

모드 요소에 의한 설정값들에 대해서는 공식문서를 참조해주세요. [[https://webpack.js.org/configuration/mode/|공식문서]]

'''Browser Compatibility'''

다양한 브라우저을 위해서 ES5 문법 지원을 도와주는 요소입니다. ([[Internet Explorer/버전 #s-1.8|IE8]] 버전 과 그 보다 낮은 버전들은 지원하지 않습니다. )

== 기타 ==

- [[React(라이브러리)|React]]의 CRA(Create-React-App) 사용하여 구성할 때 자동으로 설치된다.

- webpack 끼리 merge 할 수 있도록 도와주는 라이브러리도 존재한다. 이를 이용해 설정파일들을 경우에 따라 선택해서 번들링을 진행할 수 있다.

- 번들링이 된 파일에서 에러가 나는 부분을 찾기 위해서는 소스 맵(Source Map)을 설정할 수 있다. 이 소스 맵을 통해 원본 파일과 빌드 파일 사이에 위치를 알 수 있습니다.

== 같이 보기 ==

- [[JavaScript]]
- [[Node.js]]
- [[npm]]
