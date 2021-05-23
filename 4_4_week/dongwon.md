## Gitgub Action/Pages를 이용한 React 웹 CI/CD 설정

Gitgub Pages를 이용해 React 웹 배포를 할 때 간단히 gh-pages 패키지를 이용하지만, Github Action을 이용해 CI를 설정할 때 발생하는 gh-pages 오류를 피하기 위해 webpack을 설정하여 CI/CD를 설정해 봤습니다.

# Gitgub Actions을 이용한 CI 설정

## 1. 배포하고자 하는 레파지토리의 workflow 설정

- Actions -> Set up this workflow
  ![set workflow](https://user-images.githubusercontent.com/59330828/109997716-d557f100-7d53-11eb-9b00-cf5d5ea3723a.png)

- build.yml로 파일명 변경 -> commit
  ![create build yml](https://user-images.githubusercontent.com/59330828/110007207-caa25980-7d5d-11eb-942b-514e9a8408c1.PNG)

<br>

## 2. 레파지토리 .gitgub\workflows의 build.yml에 Workflow 작성

- [Workflow 작성 참고](https://zzsza.github.io/development/2020/06/06/github-action/)

- **Workflow 구조**

  1. master branch(개발용)에서 push 했을 때 Workflow 실행
  2. build 하기 위환 환경 설정 (node, npm, 상대경로)
  3. github page 사용하기 위한 폴더 이름 변경
  4. 배포용 브랜치 release에 변경사항 적용

- build.yml

  ```yml
  name: CI
  on:
  push:
      ##### 1 #####
      branches: [master]

      workflow_dispatch:

  jobs:
  build:
      runs-on: ubuntu-latest
      steps:
      ##### 2 #####
      # 레파지토리 check out
      - name: Checkout source code.
          uses: actions/checkout@v2

      # node.js 버전 12
      - uses: actions/setup-node@v2
          with:
          node-version: '12'

      # npm 설치
      - name: Install Dependencies
          run: npm install

      #환경변수 설정 ( 상대경로 지정 )
      - name: Set Env
          run: echo "PUBLIC_URL=/${GITHUB_REPOSITORY#*/}" >> $GITHUB_ENV

      # 빌드
      - name: Build
          run: npm run build

      ##### 3 #####
      # 폴더 이름 변경
      - name: Change folder name build to docs
          run: mv build docs

      ##### 4 #####
      # git 인증
      - uses: actions/labeler@v2
          with:
          repo-token: ${{ secrets.GITHUB_TOKEN }}

      # git remote update
      - name: Remote Update
          run: git remote update

      # git fetch
      - name: Fetch
          run: git fetch --all

      # stash 생성
      - name: Stash
          run: git stash

      # 배포용 release 생성 후 이동
      - name: create release branch
          run: git checkout -b release

      # stash 적용
      - name: Apply stash
          run: git stash pop

      # 사용자 인증
      - name: Github auth and commit
          env:
          MY_EMAIL: [사용자 이메일]
          MY_NAME: [사용자 이름]
          run: |
          git config --global user.email $MY_EMAIL
          git config --global user.name $MY_NAME

      # releaes 브랜치에 push
      - name: git commit
          run: |
          git add .
          git commit -m "Build done!"
          git push -f --set-upstream origin release
  ```

  <br>

## 3. 테스트하기

- master branch에서 변경 사항 push
- 레파지토리 Actions 탭 -> workflow 클릭
  ![action state](https://user-images.githubusercontent.com/59330828/110007423-fde4e880-7d5d-11eb-8654-1a8d2d8da755.PNG)

- 진행 완료
  ![테스트 성공](https://user-images.githubusercontent.com/59330828/110003837-21a62f80-7d5a-11eb-8064-c4a15898fb68.png)

- release branch의 docs 폴더 생성 확인

  ![check docs](https://user-images.githubusercontent.com/59330828/110018592-c6c90400-7d6a-11eb-86a6-34c1f706a163.PNG)

<br>

# Gitgub Pages을 이용한 CD 설정

> Github Pages를 이용해 build 후 생성되는 정적 파일 배포

## 1. 레파지토리 Github pages 설정

- Settings탭 -> GitHub Pages -> release branch 선택 -> /docs 폴더 선택
  ![github page setting](https://user-images.githubusercontent.com/59330828/110006692-4f40a800-7d5d-11eb-9ae2-3e0aeaa42916.PNG)

<br>

## 2. build로 생성된 정적 파일 경로 설정

- 제공되어진 url로 이동하면 흰 페이지에 오류 목록이 나옵니다.
  ![path error](https://user-images.githubusercontent.com/59330828/110012282-b9f4e200-7d63-11eb-8547-1c73f9689f7e.PNG)
- **원인 분석**

  - 이 오류는 **css나 jpg가 올바르지 않은 경로로 load 되었기 때문에 발생하는 오류입니다.**
  - Github pages를 이용하면 기본 url로 github.com/[깃허브 ID]/[레파지토리 name]가 주어집니다.
  - 이 때 build로 생성된 index.html에서는 파일들을 github.com/[깃허브 ID]/static... 로 부터 파일들을 로드하기 때문에 **github.com/[깃허브 ID]/[레파지토리 name]/static... 으로 변환하는 과정이 필요합니다.**

    ![css load](https://user-images.githubusercontent.com/59330828/110011981-5ec2ef80-7d63-11eb-9b72-712a1ab98534.PNG)

- **원인 해결**

  > 개발(start), 배포(build) 시 둘다 자동화되도록 설정했습니다.

  1. config -> paths.js의 publicUrlOrPath의 주석을 보면 homepage를 통해 path를 설정하는 것을 알 수 있습니다.

     ```javascript
     // We use `PUBLIC_URL` environment variable or "homepage" field to infer
     // "public path" at which the app is served.
     // webpack needs to know it to put the right <script> hrefs into HTML even in
     // single-page apps that may serve index.html for nested URLs like /todos/42.
     // We can't use a relative path in HTML because we don't want to load something
     // like /todos/42/static/js/bundle.7289d.js. We have to know the root.

     const publicUrlOrPath = getPublicUrlOrPath(
       process.env.NODE_ENV === "development",
       require(resolveApp("package.json")).homepage,
       process.env.PUBLIC_URL
     );
     ```

  2. require(resolveApp('package.json')).homepage : package.json 객체의 homepage값을 참조해라.
     package.json 파일의 마지막에 homepage를 추가해서 경로를 지정할 수 있습니다.

     ```javascript
     ...
         ]
     },
     homepage : "레파지토리 name"
     ```

  3. 위 방법은 배포용일 때만 해당, 개발용을 위해서는 다시 고쳐야하는 불필요한 작업이 필요합니다.
     -> 개발용, 배포용 자동화 과정 필요. confing - paths.js에서 코드 수정

     ```javascript
     const homepage =
       process.env.NODE_ENV === "development"
         ? "/"
         : require(resolveApp("package.json")).homepage;

     const publicUrlOrPath = getPublicUrlOrPath(
       process.env.NODE_ENV === "development",
       homepage,
       process.env.PUBLIC_URL
     );
     ```

  - 코드 해석
    1. script folder의 start.js (개발용), build.js(배포용)을 보면 각각 process.env.NODE_ENV값이 'development', 'production'인 것을 알 수 있습니다.
    2. publicUrlOrPath의 2번째 인자는 결국 문자열입니다. (package.json 객체의 homepage 문자열 값)
    3. 개발용일 때 process.env.NODE_ENV === 'development'일 때 '/' 경로 설정, 그 외엔 homepage 문자열 값으로 경로 설정

<br>

## 3. React Router basename 설정

- 마찬가지로 Github pages를 이용하면 기본 url로 github.com/[깃허브 ID]/[레파지토리 name]가 주어집니다.
- 기본적으로 home을 github.com/[깃허브 ID]/ 로 설정하기 때문에, 첫 시작 url을 github.com/[깃허브 ID]/[레파지토리 name]로 바꾸는 과정이 필요합니다.
- React Router의 BrowswerRouter - basename 설정
  ```javascript
  ReactDOM.render(
    <BrowserRouter basename="[레파지토리 name]">
      <App />
    </BrowserRouter>,
    document.getElementById("root")
  );
  ```
