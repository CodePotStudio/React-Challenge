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





