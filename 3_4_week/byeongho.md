# 8주차

### Next.js

- 리액트와 SPA에 SSR을 반영 할수 있도록 만들어진 프레임워크

### SPA 등장과 Next.js의 렌더링 방식

- MPA (Multi Page Application) → 모바일 UX에 적합 하지 않음.
- 모바일 환경에 적합한 SCR 기반의 SPA 방식이 각광받게 됨.
- react.js는 기본적으로 CSR방식이기 때문에 먼저 render()를 호출하여 한번 렌더링 한 후에, componentDidMount()를 통해 데이터를 받아와서 다시 한번 렌더링을 진행하는 방식이다. 하지만 여기에 next.js를 적용할 경우에는 SSR 방식으로 먼저 getInitialProps() 방식을 통해 데이터를 가져온 상태로 한번에 렌더링한다.

### Next.js의 기능

- SSR 제공을 통한 SEO(Search Engine Optimization) 향상
- Babel, Webpack 설정 커스터마이징 가능
- HMR(Hot Module Replacement)을 지원하는 웹팩 기반 환경
- Express나 Node.js와 같은 http 서버와 함께 구현 가능
- 코드스플리팅 (Code-splitting)을 통한 더욱 빠른 페이지 로드
- 간단한 클라이언트 사이드 라우팅 제공
- API route 제공
- Built-in CSS Support

### 코드 스플리팅을 통한 더욱 빠른 페이지 로드

- 용량이 커지게 되면 로딩속도가 느려지는 것을 대비하여 파일을 분리하는 코드 스플리팅을 자동으로 해준다. 필요하지 않은 컴포넌트까지 모두 불러오는 것은 비효율 적인데, Next.js는 pages 폴더에 있는 page를 처음에는 index 페이지만 불러오고 그 이후에는 각 페이지에 필요한 component만 불러오는 방식이다. 그리고 해당 페이지들은 별도로 라우팅을 해주지 않아도 링크가 된다.

### SSR 제공을 통한 SEO(Search Engine Optimization) 향상

- SSR을 통해 데이터를 다 넣어서 렌더링 해주기 때문에 검색엔진 최적화가 되어 SPA의 단점인 SEO 비효율을 상쇄 할 수 있다.

### 간단한 클라이언트 사이드 라우팅 제공

- Next.js는 React.router와 같은 Routing 기능을 자체적으로 가지고 있다. 방법은 pages 폴더에 소문자로 시작하는 파일들을 만든 후에 아래와 같은 형식으로 link만 걸어주면 된다.

```tsx
<Link href="mypage"><a><mypage><a/></Link>
```

### SSR & CSR

# **SSR**

- 서버사이드 렌더링(SSR)은 `페이지를 이동할 때마다 새로운 페이지를 요청`한다.
- 모든 탬플릿은 `서버 연산을 통해서 렌더링`되고 완성된 `페이지 형태로 응답`한다.
- 이전의 SSR(Server Side Rendering)를 사용할 때에는 변화가 있을때마다 새롭게 전체 페이지를 로드해야 했다.
- ajax가 나온 이후에는 바뀐 부분만 특정해서 변화시킬 수 있지만 동작 하나하나를 지정하기 쉽지 않았다.
- 서버에서 HTML,CSS,JS 파일을 렌더링 하는 방식은 안드로이드와 IOS와 같은 다른 플랫폼과 서버를 공유할 수 없어서 비효율적이었다.

# **CSR**

- SSR과 달리 `서버로부터 데이터를 받아서 클라이언트에 렌더링`하는 방식이다.
- 서버로부터 `데이터를 받아서 바뀐 부분의 데이터가 있는 화면만 새롭게 렌더링`한다.
- 사용자 `경험을 높여주면서 효율적으로 클라이언트 리소스를 사용`하게 한다.
- 서버가 HTML,CSS,JS 파일을 렌더링하는 대신 데이터를 보내주는 경우에는 안드로이드,IOS,웹 모바일 등 다양한 플랫폼이 서버를 공유할 수 있게 한다.
- CSR은 검색엔진 SEO 최적화를 할 수 없는 문제가 있다. 하지만 `리액트에서는 CSR과 SSR을 함께 사용`할 수 있다.이를 통해 효율적으로 서버를 운영할 수 있게 한다.

    ### NEXT.JS 시작하기

    ```tsx
    npx create-next-app nextjs-blog --use-npm --example "https://github.com/vercel/next-learn-starter/tree/master/learn-starter"
    cd nextjs-blog
    npm run dev
    ```

    ### 페이지간 이동

    - 경로 설정

    directory : post

    file name : first-post

    ```tsx

    http://localhost:3000/posts/first-post
    ```

    - 페이지가 간 이동 → route library가 필요하지 않다.

    index.js

    ```tsx
    import Link from 'next/link'

    <h1 className="title">
            Read{' '}
            <Link href="/posts/first-post">
        <a>this page!</a>
      </Link>
    </h1>
    ```

    first-post.js

    ```tsx
    import Link from 'next/link'

    export default function FirstPost() {
      return (
        <>
          <h1>First Post</h1>
          <h2>
            <Link href="/">
              <a>Back to home</a>
            </Link>
          </h2>
        </>
      )
    }
    ```

[https://ykss.netlify.app/web/next_js/#:~:text=지난 포스트에서 SSR과,주도록 만들어진 프레임워크이다](https://ykss.netlify.app/web/next_js/#:~:text=%EC%A7%80%EB%82%9C%20%ED%8F%AC%EC%8A%A4%ED%8A%B8%EC%97%90%EC%84%9C%20SSR%EA%B3%BC,%EC%A3%BC%EB%8F%84%EB%A1%9D%20%EB%A7%8C%EB%93%A4%EC%96%B4%EC%A7%84%20%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC%EC%9D%B4%EB%8B%A4).

[https://velopert.com/3293](https://velopert.com/3293)

[https://www.youtube.com/watch?v=Ujjdn2wMIew](https://www.youtube.com/watch?v=Ujjdn2wMIew)

[https://nextjs.org/](https://nextjs.org/)
