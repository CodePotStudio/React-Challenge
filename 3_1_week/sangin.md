# NextJS

> https://www.udemy.com/course/nextjs-react-the-complete-guide/  해당 강의를 수강 중 입니다 

> https://nextjs-course-blog.vercel.app/ 개인적으로 Vercel 을 이용한 배포 

> https://nextjs.org/ 공식 홈페이지


## 1. NextJS 를 왜 React 프로젝트에서 사용할까 

- 기본적으로 제공하는 CRA 프로젝트는 SEO 및 SSR 을 구성하기에 어려움이 있다. 

- CRA 방식으로 구성하면 그야말로 텅 빈 페이지에 Javascript 로 View 를 렌더링하기 때문에 구글 등에서 SEO 방식이 잘 되지 않는다. 

- 현재 CRA 를 사용하여 운영, 개발 중이 https://game.naver.com 을 참고하면 아래와 같이 body 가 비어있어서 검색 엔진이 긁어갈 요소가 없다 

![스크린샷 2021-03-25 오후 5 01 14](https://user-images.githubusercontent.com/27527229/112438865-c16c4180-8d8b-11eb-82e0-83be8f663ff0.png)

- 반면 NextJS 를 이용하여 구성을 하면 아래와 같이 소스 보기를 하면 페이지 요소가 포함되어 있다. ( https://nextjs-course-blog.vercel.app/ )

![스크린샷 2021-03-25 오후 5 05 04](https://user-images.githubusercontent.com/27527229/112439277-45bec480-8d8c-11eb-8881-bd9f49938773.png)

- 위를 참고하면 검색 엔진이 가져갈 수 있는 실제 페이지의 데이터가 NextJS 가 제공해주는 SSR 로 포함되어 있다. 이 부분이 NextJS 를 사용하는 가장 큰 이유인 것같다.
  (어드민 등 검색 엔진 최적화가 필요없는 곳에서는 NextJS 가 필요없다고 생각한다)
  
- CRA 에서 제공하는 CSR (Client Side Rendering) 과 SSR (Server Side Rendering) 의 장단점은 무엇일까? 

> SSR 의 장점은 초기 로딩 속도 및 SEO 이고, 단점은 페이지 이동 시 지속적으로 서버에 요청이 필요하다는 것이다. 

> CSR 의 장점은 초기 로딩 후 페이지 전환이 빠르고 서버에 부담이 적다. 하지만 초기 로딩 속도 및 SEO 에서 불리한 측면이 있다. 


## 2. SSR 은 과연 무엇인가 

- SSR 서버에서 사용자에게 보여줄 페이지를 모두 구성하여 페이지를 보여주는 방식 

- SSR 을 사용하게 되면 데이터가 바인딩된 페이지를 클라이언트에게 바로 보여줄 수 있다. 서버를 이용해서 페이지를 구성하기 때문에 Client Side Rendering 보다 페이지를 구성하는 속도는 늦어지지만 전체적으로 유저가 
  보는 페이지 구성을 빨라진다는 장점이 있다. 그리고 SEO 또한 쉽게 구성된다. 

![image](https://user-images.githubusercontent.com/27527229/112626079-1767e480-8e73-11eb-8827-2593bfd25945.png)

- CSR 은 SSR 보다 초기 전송되는 페이지 속도는 빠르지만 서비스에서 필요한 데이터를 클라이언트에서 추가로 요청하여 재구성하기 때문에 전체적인 페이지 완료 시점은 SSR 보다 늦어진다. 

![image](https://user-images.githubusercontent.com/27527229/112626280-572ecc00-8e73-11eb-84d6-546469b3bcfc.png)


## 3. NextJS 핵심 기능

- Server Side Rendering 

- File-based Routing
 
- Build Full Stack React Project (리액트 프로젝트 안에 서버 사이드 API 를 NodeJS 로 구성하여서 통신하기 매우 쉽다)


#### File-based Routing

- 아래와 같이 /pages/news/index.js or /pages/news.js 이렇게 파일 구조가 되어있다면 자동으로 localhost:3000/news 가 해당 페이지를 라우팅하게 된다 

![image](https://user-images.githubusercontent.com/27527229/112628950-fd300580-8e76-11eb-8bd5-cd9696716578.png)

#### Dynamic Pages 

- 아래와 같이 [newsId].js 로 파일 이름을 설정하면 동적 라우팅이 가능하다. 해당 newsId 부분에 들어가는 부분은 useRouter 를 이용하여 router.query.newsId 형식으로 가져올 수 있다. 
  즉, localhost:3000/news/3355 이면 router.query.newsId 는 3355 가 되는 것이고 이것을 이용해 서버 통신으로 해당 페이지를 구성할 수 있게 된다. 

![image](https://user-images.githubusercontent.com/27527229/112693613-49556700-8ec4-11eb-9c9b-b2132f29c96f.png)

#### 새로고침 방지 / SPA 형식으로 Next 에서 이동하기 같이 

- 아래와 같이 next/link 에 있는 Link 를 이용하여 <Link href="url"> </Link> 으로 하면 새로고침 되지 않고 SPA 방식으로 페이지 이동을 할 수 있다. 

![image](https://user-images.githubusercontent.com/27527229/112694190-460eab00-8ec5-11eb-961e-0846939a6921.png)


// 249강 까지 복습 

