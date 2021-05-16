# React Challenge 2주차

이번 기회에 써보거나 정리해보고 싶었던 개념.

다음 내용들은 챌린지가 진행되면서 추가/제거될 수 있음.

- next.js
  - [x] complete next.js tutorial
  - [x] read next.js documentation
  - [ ] next.js debugging
  - [ ] simple project with next.js
- react concept
  - [ ] About useCallback
  - [ ] lifecycle
- mobx (or redux)
- others
- [ ] socket.io (chat in react)
- [ ] css tools (styled component?)
- [ ] infinite scroll & lazy loading

## next.js tutorial

### Dynamic routes

외부 데이터에 따라 page path가 변하는 경우 [dynamic route](https://nextjs.org/docs/routing/dynamic-routes) 사용 가능

![image](https://nextjs.org/static/images/learn/dynamic-routes/page-path-external-data.png)

이전에 만든 블로그 포스트의 url을 dynamic route로 구현 예시

- 각각의 post들을 `/posts/<id>`로 생성
- `ssg-ssr.md`는 `/posts/ssg-ssr`, `pre-rendering.md`는 `posts/pre-rendering`으로 생성

전체적인 흐름은 다음과 같음

- `pages/posts` 하위에 `[id].js` 생성
  - `[]`로 감싸진 페이지는 next.js에서 dynamic route를 의미함
- 해당 파일 안에 post page를 렌더할 컴포넌트 추가
- 해당 파일에 `getStaticPaths`를 사용하여 `id`로 사용할 값 리스트 반환
- `getStaticProps` 사용하여 필요한 `id`값을 가져옴

정리하면 다음과 같음

`/posts/<id>`로 dynamic route를 구현하고 싶으면
- `<id>`가 dynamic한 값이 됨
- `/pages/posts/[id].js` 생성: 이 파일은 다음으로 구성
  - 해당 페이지를 렌더할 react component
  - `getStaticPaths`: possible values for id
  - `getStaticProps`; fetch necessary data for the post with id

tutorial의 예시가 아주 깔끔한 코드라서 한 번 따라해보면 좋을듯.


### Render Markdown, Polishing

이부분은 `remark` 라이브러리를 사용하여 markdown을 렌더링하는 부분과 css로 꾸미는 부분이다.

딱히 next.js와 관련이 없는거같아서 정리하지 않았다.

### Dynamic Route detail


- `getStaticPaths`도 `getStaticProps`와 마찬가지로 development 환경일 때에는 매 요청마다 실행되고, production 환경일 경우에는 빌드타임에 실행된다.
#### `Fallback` option: `getStaticPaths`에 줄 수 있는 옵션.
- false로 실행되면 route가 발견되지 않으면 404
- true라면 getStaticProps의 실행방식이 변경됨
  - `getStaticPaths`가 최초 build time에 실행됨
  - 만약 buildtime에 생성된 path가 아니라면 404대신 첫 요청으로 생성도니 "fallback" 버전의 페이지가 서빙됨
  - `fallback: 'blocking'` option도 있음

#### Catch-all routes
- `...`을 추가해서 extend path 가능
- `pages/posts/[...id].js`: `/posts/a`, `/posts/a/b`, `/posts/a/b/c`... 모두 match
- 이를 받기 위해서 `getStaticPaths`에서 `id` 키로 array를 반환해야됨

```
return [
  {
    params: {
      // Statically Generates /posts/a/b/c
      id: ['a', 'b', 'c']
    }
  }
  //...
]
```

물론 `getStaticProps`도 array를 받게된다.

#### Router

next.js router에 직접 접근하고 싶다면 `useRouter` hook을 사용할 수 있음. ([next/router](https://nextjs.org/docs/api-reference/next/router))

#### Custom 404

`pages/404.js`를 만들면 빌드타임에 생성된 이후에 서빙됨.


### API Routes

next.js 앱 안에서 API endpoint를 만들 수 있게 해주는 기능 (??)

`pages/api` 안에 함수를 생성해서 만들 수 있다.

> 어디에 쓰일지 감이 안오긴한다.

```
// req = HTTP incoming message, res = HTTP server response
export default function handler(req, res) {
  // ...
}
```

hello를 return하는 간단한 `hello.js` 예시

```
export default function handler(req, res) {
  res.status(200).json({ text: 'Hello' })
}
```

> request는 nodejs의 [http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage), response는 [http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse) 객체


### API Routes detail

#### `getStaticProps`이나 `getStaticPaths`에서 API Route를 fetch하지 말것
- 대신 getStaticPaths, getStaticProps에서 직접 server-side code를 적으라고 함
- getStaticProps, getStaticPaths는 client-side가 아닌 server-side에서 실행되기 때문
- ?? 그래도 왜 안되는지 잘 모르겠음.

#### good usecase: handling form input

form input을 힌들링 할 때 API Route로 POST 요청을 보내서 client bundle에 포함시키지 않고 server-side code 작성 가능

```
export default function handler(req, res) {
  const email = req.body.email
  // Then save email to your database, etc...
}
```

일반적으로 backend api를 콜하면 될것같은데 왜 좋은 예시인지 잘..


#### Preview mode

Static Generation은 headless CMS에 적합하지만 수정사항에서 preview를 보기에는 적합하지 않음 (빌드타임에 generate 되므로)

이를 위해서 API route를 활용한 preview mode를 제공한다고 함. markdown preview 같은거 만들 때 써보면 재미있을듯.

### Deploying next.js app

vercel에 배포하는 예시. 굳이 vercel을 써서 좋은점은 잘 모르겠지만 personal로 쓰면 무료라서 한번 배포해봄.

확실히 무조건 서버가 필요하다는 점이 next.js의 가장 큰 단점인거같다.

- github에 push
- vercel 계정 만들기
- github repository 지정한 이후에 배포

정말 간편하게 배포가 되긴 한다.

main branch에 머지되었을 때 빌드, 배포도 자동으로 됨.. 사용성은 아주 좋은듯하다.


아래는 vercel로 배포된 next.js blog 예시이다.

https://nextjs-tutorial-iota-rouge.vercel.app/



# next.js documentation

[next.js 문서](https://nextjs.org/docs/getting-started)

문서는 전부 정리하지는 않고 잘 모르거나 하는 개념만 정리해둘 예정

-> 딱히 전부 읽을 필요 없이 필요할 때 필요한 부분을 읽는게 좋을듯.

## Basic features

### pages
- static generation (recommended)
  - production에서 `next build` 명령어를 실행시킬 때 HTML이 생성됨 (build time)
- server-side rendering
  - 각 request마다 HTML 생성됨.

### [Environment variables](https://nextjs.org/docs/basic-features/environment-variables)

- `.env.local`로 환경변수 로드 가능
- 환경변수를 브라우저에 노출 가능

#### Loading Env

next.js에는 `.env.local`의 환경변수를 `process.env`로 로드해주는 빌트인 기능이 있음.

아래는 `.env.local`의 예시.

```
DB_HOST=localhost
DB_USER=myuser
DB_PASS=mypassword
```

이는 node.js 환경에서 자동으로 `process.env.DB_HOST`의 형태로 로드됨.

이를 data fetch나 API routes에서 사용이 가능함.

`getSteaticProps`에서 환경변수를 사용한 예

```
// pages/index.js
export async function getStaticProps() {
  const db = await myDB.connect({
    host: process.env.DB_HOST,
    username: process.env.DB_USER,
    password: process.env.DB_PASS,
  })
  // ...
}
```

> server-only secret을 보호하기 위해서 Next.js는 `process.env.*`를 빌드타임에 해당 변수로 대치시킴.

> 즉, `process.env`는 javascript object가 아니기 때문에 object destructuring을 할 수 없음. 변수로 할당해서 쓸 수 없고 `process.env.*` 형태로만 사용해야한다.


next.js는 `.env*` 파일의 `$VAR` 변수를 자동으로 expand 해줌. `$` 캐릭터를 사용하려면 escape 하면 됨 (`\$`)

```
# .env
HOSTNAME=localhost
PORT=8080
HOST=http://$HOSTNAME:$PORT
```

### Exposing environment variable to browser

default로 `.env.local`의 모든 환경변수는 node.js 환경에서만 사용 가능하기 때문에 브라우저에 노출되지 않음.

이를 노출시키기 위해서는 `NEXT_PUBLIC_` prefix를 사용하면 됨.

```
NEXT_PUBLIC_ANALYTICS_ID=abcdefghijk
```

이는 node.js 환경에서 `process.env.NEXT_PUBLIC_ANALYTICS_ID`로 자동으로 로드되고, 코드에서 사용 가능.

뿐만 아니라 브라우저에서도 사용 가능하기 때문에 빌드타임 이후에도 사용 가능.

```
// pages/index.js
import setupAnalyticsService from '../lib/my-analytics-service'

// NEXT_PUBLIC_ANALYTICS_ID can be used here as it's prefixed by NEXT_PUBLIC_
setupAnalyticsService(process.env.NEXT_PUBLIC_ANALYTICS_ID)

function HomePage() {
  return <h1>Hello World</h1>
}

export default HomePage
```

### Default Environment Variables

대개는 `.env.local` 파일만 필요하지만, 종종 dev 환경이나 production 환경에서 env를 추가해야할 경우가 생김. (`next dev`, `next start`)

next.js는 default environment를 다음과 같이 사용하게 해줌
- `.env`: all environment
- `.env.development`: development environment
- `.env.production`: production environment

`.env.local`은 항상 default를 override함.

> `.env*.local`이 secret을 보관하고, `.gitignore`에 추가되어야한다. `.env`, `.env.development`, `.env.production`은 기본으로 레파지토리에 저장함.

아주 헷갈리게 되어있는듯.. 이부분 안봤으면 당연히 `env.production`에 secret들 넣었을듯


### Environment variables on vercel

vercel로 배포한다면 (아마 거의 그렇겠지만) project settings에서 env variable을 설정할 수 있음.

## Routing

### Dynamic routes

`[param]` 형태로 dynamic route를 지원

이 경우 `router.query`로 해당 파라미터들을 받아올 수 있음.

`/pages/post/[pid].js`의 예시

```
import { useRouter } from 'next/router'

const Post = () => {
  const router = useRouter()
  const { pid } = router.query

  return <p>Post: {pid}</p>
}

export default Post
```

url에 해당하는 query 객체가 생성됨.

`/posts/abc?foo=bar`의 경우


```
{
  "foo": "bar",
  "pid": "abc"
}
```

#### Catch all routes

`...`을 사용해서 모든 path를 캐치할 수 있음.

`pages/post/[...slug].js`는 아래 모두 매칭.

- /post/a
- /post/a/b
- /post/a/b/c

이는 query object에 array로 추가됨.

```
{"slug": ["a", "b", "c"]}
```

#### Optional catch all routes

위에서는 한 개 이상의 slug가 필수적이였는데, double bracket을 사용해서 이를 optional로 쓸 수도 있다.

`pages/post/[[...slug]].js`는 slug가 없는것도 매칭됨.
- /post
- /post/a
- /post/a/b
- /post/a/b/c

#### 주의사항

- predefined route는 dynamic route보다 우선권을 가짐. (dynamic route가 모든 route를 캐치한다고 해도)
  - pages/post/create.js - Will match /post/create
  - pages/post/[pid].js - Will match /post/1, /post/abc, etc. But not /post/create
  - pages/post/[...slug].js - Will match /post/1/2, /post/a/b/c, etc. But not /post/create, /post/abc

### Shallow routing

shallow routing은 data fetching method를 실행시키지 않고도 url을 변경할 수 있게 해줌. (`shallow: true`)

아래는 `?counter=10`으로 업데이트하는 예시

```
import { useEffect } from 'react'
import { useRouter } from 'next/router'

// Current URL is '/'
function Page() {
  const router = useRouter()

  useEffect(() => {
    // Always do navigations after the first render
    router.push('/?counter=10', undefined, { shallow: true })
  }, [])

  useEffect(() => {
    // The counter changed!
  }, [router.query.counter])
}

export default Page
```

url이 변경되는 것을 `componentDidUpdate`에서도 볼 수 있음.

```
componentDidUpdate(prevProps) {
  const { pathname, query } = this.props.router
  // verify props have changed to avoid an infinite loop
  if (query.counter !== prevProps.router.query.counter) {
    // fetch data based on the new query
  }
}
```

#### 주의사항

shallow routing은 같은 페이지 URL에서만 작동함. 새로운 페이지 url로 변경되면 해당 페이지가 로드되기 떄문.

그래서 아래와 같이는 쓸 수 없음.

```
router.push('/?counter=10', '/about?counter=10', { shallow: true })
```

## API Routes

next.js로 API를 만들수 있게 해줌. 굳이 쓸 일이 있을까?

dynamic API Routes, API Middleware 등도 제공하긴 하는데 나중에 필요할때 보면될듯.


## Authentication

authentication은 어떤 data-fetching 전략을 사용하는지에 따라서 달라질 수 있음.

- Use static generation to server-render a loading state, followed by fetching user data clinet-side
- Fetch user data server-side to eliminate a flash of unauthenticated content

### Authenticating Statically generated pages

next.js는 특별한 blocking data requirement가 없다면 (`getServerSideProps`, `getInitialProps` 등이 없다면) 해당 페이지를 static하다고 결정함.

대신 페이지가 server에서 loading state로 렌더한 이후, clinet-side에서 user를 fetching하면 됨.

장점은 페이지를 `next/link`를 사용해서 CDN으로 서빙 가능 -> fastser TTI (time to interactive)

아래는 이렇게 구현된 profile page.

```
// pages/profile.js

import useUser from '../lib/useUser'
import Layout from '../components/Layout'

const Profile = () => {
  // Fetch the user client-side
  const { user } = useUser({ redirectTo: '/login' })

  // Server-render loading state
  if (!user || user.isLoggedIn === false) {
    return <Layout>Loading...</Layout>
  }

  // Once the user request finishes, show the user
  return (
    <Layout>
      <h1>Your Profile</h1>
      <pre>{JSON.stringify(user, null, 2)}</pre>
    </Layout>
  )
}

export default Profile
```

### Authenticating Server-rendered page

`getServerSideProps`를 사용하면 next.js는 각 요청에 페이지를 pre-render하고 데이터를 props로 반환함.

```
export async function getServerSideProps(context) {
  return {
    props: {}, // Will be passed to the page component as props
  }
}
```

이전 profile page 예시를 server-side rendering으로 수정한 예시.

```
// pages/profile.js

import withSession from '../lib/session'
import Layout from '../components/Layout'

export const getServerSideProps = withSession(async function ({ req, res }) {
  // Get the user's session based on the request
  const user = req.session.get('user')

  if (!user) {
    return {
      redirect: {
        destination: '/login',
        permanent: false,
      },
    }
  }

  return {
    props: { user },
  }
})

const Profile = ({ user }) => {
  // Show the user. No loading state is required
  return (
    <Layout>
      <h1>Your Profile</h1>
      <pre>{JSON.stringify(user, null, 2)}</pre>
    </Layout>
  )
}

export default Profile
```

이 패턴의 장점은 redirect 되기 전 unauthenticated content에 대해서 생각할 필요가 없다는점.

대신 `getServerSideProps`에서 user를 가져오기 전까지 rendering이 블락되기 때문에 authentication lookup이 빨라야한다.

### Authentication providers

#### Own Database

- [with-iron-session](https://github.com/vercel/next.js/tree/canary/examples/with-iron-session)
  - low-level, encrypted, stateless session
- [next-auth](https://github.com/nextauthjs/next-auth-example)
  - full-featured authentication system with built-in providers (Google, FB, GH), JWT, email/password ...
  - 웬만하면 next-auth 쓸듯?

- [passport](http://www.passportjs.org/)라는것도 있다고함.

그 이외 firebase, [magic](https://magic.link/)(passwordless auth), Auth0, superbase 등등에 대한 예시가 잘 나와있음.


## [Advanced features](https://nextjs.org/docs/advanced-features/preview-mode)

이건 굳이 지금 읽을필요 없을거같고 뭐뭐 있는지정도 알고 있으면 좋을듯.

- preview mode: 말그대로 preview mode.
- dynamic import
  - ES2020의 [dynamic import](https://github.com/tc39/proposal-dynamic-import) 기능을 지원한다고함.
  - 뭔지 나중에 한번 보면 좋을듯.
- Automatic  static optimization
  - 어떻게 next.js가 페이지가 static인지 결정하는지
- Static HTML Export
  - `next export` -> app을 static HTML로 export 해줌 (node.js없는 standalone)
  - 이럴거면 굳이 next.js 쓸이유가..
- Absolute imports and module path aliases
  - 이건 실제로 사용하면 import가 훨씬 깔끔해질듯?

```
// tsconfig.json or jsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/components/*": ["components/*"]
    }
  }
}
```
```
// components/button.js
export default function Button() {
  return <button>Click me</button>
}
```
```
// pages/index.js
import Button from '@/components/button'

export default function HomePage() {
  return (
    <>
      <h1>Hello World</h1>
      <Button />
    </>
  )
}
```
- AMP Support
  - React page를 AMP page로 변경 가능
  - [AMP](https://amp.dev/)는 처음 들어봤는데 Accelerated Mobile Page라고 함.
- Customizing Babel config
- Customizing PostCSS config
- Custom server
  - `next start`로 시작되는 next.js의 node.js 서버를 custom 할수있음
  - custom server는 vercel에 배포할수 없다고함.
- Custom `App`
  - `./pages/_app.js`에서 `import App from 'next/app'`으로 import 해서 customize 할수있음
- Custom `Document`
  - html, body 태그 등을 변경하기 위해서 쓰임.
- Custom Error page
  - `pages/404.js`, `pages/500.js` 등
- `src` Directory
  - root `pages` 디렉토리 말고 `src/pages`처럼 쓰고싶은 사람들을 위해 넣어놓은듯
- Multi zones
  - zone: single deployment of a next.js app
  - 여러 zone을 한개의 앱으로 합칠 수 있따고함 (?)
- Measuring performance 
  - vercel로 배포하면 이것저것 제공한다고함
- [Debugging](https://nextjs.org/docs/advanced-features/debugging)
  - chrome devtools, vscode debugger
  - next.js debug mode로 실행시킬 수 있음 (`--inspect` flag)
  - 그 이후 debugger에 연결 (`chrome://inspect`)
  - [debugger](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/debugger) statement를 frontend/backend 코드에 모두 사용 가능. breakponit처럼 사용 가능
  - 이부분은 한번 따로 정리해서 실제로 사용해보면 좋을듯
- Source maps
- Codemods
- Internationalized routing
