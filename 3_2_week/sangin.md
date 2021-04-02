https://github.com/BearCola/React-Challenge/blob/main/3_1_week/sangin.md

에 이어서 작성 


- 아래와 같이 Meetup Item 페이지에서도 이동할 때 next/router 의 useRouter 를 이용하여 push 로 이동한다. 이 부분은 next 에서 제공하는 Link 태그와 같은 효과를 제공한다. 

![스크린샷 2021-04-03 오전 6 13 19](https://user-images.githubusercontent.com/27527229/113455505-c1340c00-9445-11eb-9153-5e6e78f777bd.png)


- 페이지 Pre-Rendering 은 아래와 같은 과정으로 진행된다. 

<img width="1752" alt="스크린샷 2021-04-03 오전 6 35 41" src="https://user-images.githubusercontent.com/27527229/113456214-95198a80-9447-11eb-8254-0651a0bdf5da.png">


- 기존의 React 애서는 서버 데이터를 가져올 때, useEffect Hook 을 이용해서 데이터를 가져온다. 하지만 그 방식으로 할 경우 서버사이드 렌더링이 되지 않아서 SEO 에 좋지 않다. 따라서 NextJS 에서는 아래와 같이 
  getStaticProps 를 이용해서 서버 데이터를 가져온다. (예시는 mongo db 를 이용했다.)

![스크린샷 2021-04-03 오전 6 37 53](https://user-images.githubusercontent.com/27527229/113456297-cb570a00-9447-11eb-9cb0-92ef9ba6947d.png)


- 위와 같이 getStaticProps 를 이용하면 이전에 빈 페이지 였던 페이지 소스 보기에서 아래와 같이 title, address 등이 이미 렌더링 되어 있는 것을 확인할 수 있고 이 정보들이 구글 등이 SEO 등을 할 때 유리한 점으로 작용한다. 

<img width="1807" alt="스크린샷 2021-04-03 오전 6 40 58" src="https://user-images.githubusercontent.com/27527229/113456364-0e18e200-9448-11eb-9abf-6f6d8a0a927b.png">

- npm run build 를 nextjs 프로젝트에서 실행하면 아래와 같이 미리 제공되는 페이지 등을 알 수 있다.

<img width="803" alt="스크린샷 2021-04-03 오전 6 48 39" src="https://user-images.githubusercontent.com/27527229/113456613-ac0cac80-9448-11eb-83cb-9290ab781917.png">

<img width="1319" alt="스크린샷 2021-04-03 오전 6 48 50" src="https://user-images.githubusercontent.com/27527229/113456621-b169f700-9448-11eb-8bad-4f129173ec6e.png">

- 아래와 같이 revalidate 10 을 주게 되면 해당 페이지는 10초 마다 데이터를 갱신하게 된다. 데이터가 보통 달라지는 주기에 따라서 해당 페이지에 아래 옵션만 설정해두면 데이터를 해당 시간에 맞게 최신으로 갱신하게 된다. 

<img width="915" alt="스크린샷 2021-04-03 오전 6 51 43" src="https://user-images.githubusercontent.com/27527229/113456840-4a007700-9449-11eb-9a84-e7501d78eb97.png">
