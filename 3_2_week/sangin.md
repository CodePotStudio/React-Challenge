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

- getServerSideProps 는 빌드될 때 관여하지 않고 항상 서버에서 가져온다. 빌드 이후에도 계속 데이터의 변경이 필요한 경우 사용하지만 보통은 getStaticProps 로 가능한 경우가 많아서 각 request 마다 계속 바껴야하는 필수적인 페이지가 아닌 경우에는 사용을 피하는 것이 좋을 듯 하다. 각 request 마다 데이터를 기다려야 하는 상황이고 초마다 데이터가 바뀌는 상황이 아니라면 getStaticProps 를 고려해보는 것이 먼저일 것 같다. (CDN 캐싱 등을 고려했을 때에도 getStaticProps 가 좋다)

![스크린샷 2021-04-03 오전 7 22 09](https://user-images.githubusercontent.com/27527229/113458479-57b7fb80-944d-11eb-826d-70e5e507491c.png)

- getStaticProps 는 빌드 프로세스에서 Pre Generated 된다. 따라서 유저가 해당 페이지를 진입하는 시점이 아닌 빌드할 때 어떤 페이지 Path 가 있어야 하는지 NextJS 가 알 수 있어야 한다. 그렇기 때문에 getStaticPaths 의 구현이 필요하다. fallback 옵션을 통해서 false 를 설정할 경우 paths 에 없는 요청이 들어온 경우 404를 보여주고 true 로 구성하면 대체 페이지를 보여준 다음 이후에 서버 응답을 받은 후 페이지를 보여줄 수 있다. 추가로 fallback blocking 도 추가되었는데 blocking 을 설정하면 대체 페이지가 아닌 서버에서 데이터가 올 때까지 기다리게 된다. 

![스크린샷 2021-04-03 오전 7 57 08](https://user-images.githubusercontent.com/27527229/113459998-373e7000-9452-11eb-8c91-437f15c4bdaa.png)

- NextJS 를 이용하면 아래와 같이 API 구성도 쉬워진다. POST /api/new-meetup 을 pages/api/new-meetup.js 에 handler 메소드로 아래와 같이 구현해두면 컴포넌트에서 /api/new-meetup 을 POST 요청하여 실제 데이터를 데이터베이스에 넣을 수 있다. 

![스크린샷 2021-04-03 오전 8 03 10](https://user-images.githubusercontent.com/27527229/113460343-6bfef700-9453-11eb-90e6-32f6efe3f6b8.png)

- 위 API 를 다음과 같이 POST 요청을 보내서 등록시킬 수 있다. 

![스크린샷 2021-04-03 오전 8 22 45](https://user-images.githubusercontent.com/27527229/113461038-df096d00-9455-11eb-88ca-65fa842070e8.png)

- Head 로 meata 데이터 및 title 추가하기 (Search Engine 등에 meta 데이터 및 title 제공)

![스크린샷 2021-04-03 오후 12 10 21](https://user-images.githubusercontent.com/27527229/113466296-9b266000-9475-11eb-834b-c335085aeaef.png)

![스크린샷 2021-04-03 오후 12 13 15](https://user-images.githubusercontent.com/27527229/113466338-fd7f6080-9475-11eb-9d35-64bb85e03d20.png)

- Props 에서 데이터를 가져와서 동적으로 Meta 데이터 구성 

![스크린샷 2021-04-03 오후 12 13 49](https://user-images.githubusercontent.com/27527229/113466352-12f48a80-9476-11eb-96b3-1d304a513877.png)



  
