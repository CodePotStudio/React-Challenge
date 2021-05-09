# React Challenge 1주차

이번 기회에 써보거나 정리해보고 싶었던 개념.

다음 내용들은 챌린지가 진행되면서 추가/제거될 수 있음.

- [ ] next.js
- [ ] mobx (or redux)
- [ ] lifecycle
- [ ] css tools (styled component?)
- [ ] infinite scroll & lazy loading
- [ ] socket.io (chat in react)


# Next.js

1주차는 next.js로 정했다. 요즘 정말 핫하기도 하고, 여기저기에서 많이 쓰이니 나중에 제대로 쓰기 전에 next.js와 SSR에 대해서 정리할 필요성을 느껴서.

## Next.js 개념

> Next.js is an open-source React front-end development web framework created by Vercel that enables functionality such as server-side rendering and generating static websites for React based web applications

위키피디아에 있는 설명인데 깔끔하고 명시적인것같아 가져와봤다.

- framework
- server-side rendering

### server-side rendering이란?

사실 백엔드 개발자라면 서버사이드렌더링은 낯설지 않을 것이다. 다만 전형적인 CSR (Client-Side Rendering)인 리액트에서 SSR을 사용한다는것이 잘 와닿지 않아 좀 탐색해보았다.

용어가 조금 헷갈리긴 하지만 React, 즉 frontend에서 SSR을 사용할 경우 백엔드 서버가 아닌 프론트용 서버를 두고 렌더링을 서버사이드에서 하여 내려준다.


## next.js 튜토리얼

### create-next-app

next.js에서 제공하는 간단한 blog 튜토리얼을 진행해보았다

```
npx create-next-app nextjs-blog --use-npm --example "https://github.com/vercel/next-learn-starter/tree/master/learn-starter"
```

> 실제로 cra처럼 사용할때에는 `npx create-next-app`을 사용하면 될듯.

기본적인 경로는 다음과 같다.

```
$ tree -L 1
.
├── README.md
├── node_modules
├── package-lock.json
├── package.json
├── pages
└── public
```

일반적인 CRA와 비슷하지만 `pages`라는 디렉토리가 있는게 특이하다.


실제로 서버를 띄우고 이것저것 만져보자.

```
npm run dev
```

### navigate between pages

next.js에서는 `page`라는 개념이 사용된다. 이는 `pages` 디렉토리 하위에서 exported되는 컴포넌트로, 파일명에 기반해서 라우팅이 된다.

위의 blog 예시의 pages 디렉토리는 다음과 같다.

```
$ tree pages
pages
└── index.js
```

- `pages/index.js`: index는 `/`로 routing된다.
- `pages/posts/first-post.js`는 `/posts/first-post`로 라우팅된다.

기존 프로젝트에는 index.js만 있으니 `pages/posts/first-post.js`를 생성하고 라우팅 해보자.

간단하게 `first-post.js`를 아래와 같이 생성하고 `/posts/first-post` url을 입력하면 해당 페이지로 라우팅되는것을 볼 수 있다.

```
export default function FirstPost() {
  return <h1>First Post</h1>
}
```

#### Link component

next.js에서 페이지간의 이동은 <a>태그를 래핑하고 있는 `Link` 컴포넌트를 사용한다.

사용법은 심플.

`pages/index.js`의 title 부분을 변경해보자.

```
import Link from 'next/link'
...
<h1 className="title">
  Read{' '}
  <Link href="/posts/first-post">
    <a>this page!</a>
  </Link>
</h1>
...
```

그리고 위에서 만들었떤 `first-post.js`에도 홈으로 돌아가는 link를 추가해보자.

```
import Link from 'next/link'

export default function FirstPost() {
  return (
    <>
      <h1>First post</h1>
      <h2>
        <Link href="/">
          <a>Back to home</a>
        </Link>
      </h2>
    </>
  )
}
```

`Link` 컴포넌트는 client-side navigation을 가능하게 해준다고 한다.

> client-side-navigation이란? page transition이 브라우저가 아닌 javascript에서 일어나는 것. full refresh가 되지 않아서 더 빠르다고함.

> 실제로 개발자도구에서 background color를 바꾸고 routing 해보면 다른 페이지로 넘어가도 변경된 css가 바뀌지 않는다.

![image](https://nextjs.org/static/images/learn/navigate-between-pages/client-side.gif)

pages는 다양한 장점이 있다고 소개된다.
- code splitting: net.js가 자동으로 code splitting을 적용하여 해당 페이지에 필요한 코드만 가져옴
  - 수백 페이지가 있어도 홈 로딩에 크게 오랜 시간이 걸리지 않을 수 있음
- prefetching: `Link` 컴포넌트가 브라우저 뷰포트에 나타나면 next.js가 백그라운드에서 해당 페이지에 필요한 코드를 prefetch해줌
  - 해당 페이지를 클릭했을때 이미 로드되어있기 때문에 빠르게 페이지 로드 가능

확실히 ssr이 필요하거나 많은 페이지를 가진 애플리케이션에 적합해보인다.

### Assets, Metadata, and CSS

next.js에서는 CSS, Sass를 built-in으로 지원함.

- `public/` 디렉토리 안에 asset을 넣고 사용할 수 있음.
- 특이한건 [next/image](https://nextjs.org/docs/api-reference/next/image) 컴포넌트를 제공해서 이를 사용할 수 있다는점이다. 실제로 얼마나 쓰일지는 잘 모르겠다.
_ next.js는 build time에 이미지 최적화를 하지 않고 유저의 요청이 있을때 on-demand로 최적화를 하기 때문에 빌드 시간이 줄어든다고 함. SSR의 또다른 장점이라고 할 수 있겠따.
- 이미지는 디폴드로는 lazy load된다. (뷰포트에 들어오면 로드)

그 이외에 [next/head](https://nextjs.org/docs/api-reference/next/head)도 제공함.

#### css

- css는 vercel의 [style-jsx](https://github.com/vercel/styled-jsx)가 내장되어있다.
- global css는 `pages/_app.js`에서만 불러와서 사용 가능함.
- 보통 convention으로 root directory에 `styles` 디렉토리를 만들고 `global.css`를 넣어서 사용

### Pre-rendering and data fetching

[Pre-rendering](https://nextjs.org/docs/basic-features/pages#pre-rendering)
- next.js는 모든 페이지를 pre-rendering 함
  - client-side의 javascript에서 렌더링하지 않고 각 페이지의 html을 미리 만들어놓고 서빙함
  - 더 나은 퍼포먼스와 SEO 가능
- 렌더링된 각 페이지의 HTML은 페이지에 필수적인 최소한의 javascript 코드를 가지고 있음.
- 브라우저에 페이지가 로드되면 코드가 동작하여 페이지를 interactive하게 만들어줌

실제로 브라우저에서 javascript를 disable 하고 앱을 실행해보면 pre-rendering이 실행되기 떄문에 javascript 없이도 앱이 로드되는것을 볼 수 있다.

![image](https://nextjs.org/static/images/learn/data-fetching/pre-rendering.png)

![image](https://nextjs.org/static/images/learn/data-fetching/no-pre-rendering.png)

#### two forms of pre-rendering

next.js는 두가지 방법의 pre-rendering을 제공해줌.

- [Static Generation](https://nextjs.org/docs/basic-features/pages#static-generation-recommended)
  - build time에 HTML을 생성하는 방식. 각 request에서 재사용됨
- [Server Side Rendering](https://nextjs.org/docs/basic-features/pages#server-side-rendering)
  - 각 request에 HTML을 생성하는 방식

![image](https://nextjs.org/static/images/learn/data-fetching/static-generation.png)

![image](https://nextjs.org/static/images/learn/data-fetching/server-side-rendering.png)


기본적으로 next.js는 각 페이지에 어떤 방식의 pre-rendering을 사용할지 선택 가능하게 되어있다.

각각을 언제 사용해야할까?

문서에 따르면 가능하면 Static generation을 사용하라고 권장
- 한번 빌드되고 나서는 CDN으로부터 서빙되기 떄문에 SSR보다 훨씬 빠름
- 유저의 요청 이전에 pre-render 할 수 있다면 Static generation을 사용

페이지가 자주 업데이트 되거나 유저의 요청에 따라 컨텐츠가 변한다면 SSR

#### Static Generation

Static generation은 데이터 없이도 가능하지만, 외부 데이터를 fetch하고도 가능하다.

`getStaticProps`를 async로 사용.
- 빌드타임에 getStaticProps가 실행되고
- 그때 필요한 데이터를 fetch해서 페이지로 props를 보내줌

> development mode에서는 getStaticProps는 매 request마다 실행된다고 한다.

실제로 getStaticProps을 사용해보자. blog data로 생성한 예시 코드.
- pages/index.js에 추가
- getSortedPostsData는 data를 fetch해서 반환하는 함수

```
import { getSortedPostsData } from '../lib/posts'

export async function getStaticProps() {
  const allPostsData = getSortedPostsData()
  return {
    props: {
      allPostsData
    }
  }
}

export default function Home({ allPostsData }) {
...
```

home 컴포넌트에서 allPostsData를 props로 사용이 가능하다.

- 위 예시에서는 디렉토리의 마크다운 파일을 읽었지만, api나 database에서 직접 쿼리하는것도 가능하다.
  - 그 이유는 getStaticProps가 무조건 server-side에서만 실행되기 때문
- getStaticProps는 반드시 `page`에서만 사용되어야 한다.

#### Server side rendering: Fetching data at request time

만약 build time이 아니라 request time에 데이터를 가져오고 싶다면 SSR을 사용해야한다.

![image](https://nextjs.org/static/images/learn/data-fetching/server-side-rendering-with-data.png)

이를 위해서 `getStaticProps` 대신 `getServerSideProps`를 사용하면 됨.

`getServerSideProps`는 매 request time에 실행되기 때문에 `context`는 request에 특화된 정보를 담고있다.

```
export async function getServerSideProps(context) {
  return {
    props: {
      // props for your component
    }
  }
}
```

> TTFB (Time To First Byte)가 CDN에서 캐시되는 static props보다 느리기 때문에 데이터가 반드시 request time에 fetch 되어야 하는 경우에만 사용하는게 좋다.

#### client-side rendering

data를 pre-render할 필요가 없다면 [Client-side rendering](https://nextjs.org/docs/basic-features/data-fetching#fetching-data-on-the-client-side) 전략을 사용하는것도 좋음

![image](https://nextjs.org/static/images/learn/data-fetching/client-side-rendering.png)

위 그림에서 설명된 것처럼
- 외부 데이터 없이 페이지를 render 한 뒤,
- 페이지가 로드될 때 javascript에서 데이터를 fetch해온 뒤 나머지를 그려줌
- 일반적인 react 애플리케이션에서 실행되는 방식

다만 이 경우에는 next.js에서 제공하는 [SWR](https://swr.vercel.app/) hook을 사용할것을 권장하고 있다.
- caching, revalidation, focus tracking, refetching on interval 등을 제공해준다고 함.

```
import useSWR from 'swr'

function Profile() {
  const { data, error } = useSWR('/api/user', fetch)

  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <div>hello {data.name}!</div>
}
```

#### 정리
페이지를 pre-rendering 하는 방식
- static generation: build time에 모든 html을 렌더링 하고 CDN에서 서빙
- server-side rendering: request time에 html을 렌더링하는 방식

데이터를 fetch하는 방식
- Fetch data from server
  - build time
    - getStaticProps: build time에 (static generation) 데이터를 fetch해서 props로 넣어주는 방식
  - request time
    - getServerSideProps: request time에 (server-side rendering) 데이터를 fetch
- Fetch data from client
  - useSWR: pre-render시에 데이터를 fetch하지 않고 client-side에서 데이터를 fetch

> 작성한 코드는 [nextjs-tutorial repository](https://github.com/bartkim0426/nextjs-tutorial)에 올려두었다.

## 다음 주차에 진행할 내용

- 튜토리얼에서 다 진행 못한 dynamic routes, api routes, deploying
- read next.js documentation

