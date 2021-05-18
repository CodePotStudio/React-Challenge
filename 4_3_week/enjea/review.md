# 챌린지 3주차 리뷰

# 1. 컴포넌트 초기 생성 시(초기 렌더링 시 필요한 데이터를 끌어오는 로직을 쓸 때)
> constructor(props) {
>  super(props)
> }  
// 컴포넌트 생성자 메소드
> UNSAFE_componentWillMount(){
> }
// 컴포넌트 렌더링 직전에 호출됨. 주로 서버사이드에서 데이터를 호출하는 용도로 썼다가 용도폐기로 deprecated 됬다고 함. 아직까지는 사용가능. 
// v17 전까지는 deprecated된 메소드들을 unsafe를 생략하고 사용할 수 있다. 
> componentDidMount(){
> } 
// 컴포넌트 렌더링 시에 호출 됨.  주로 외부 라이브러리 연동시, 컴포넌트에 필요한 데이터 ajax 요청시, DOM 관련 작업에 사용함.
예시를 들자면 DOM을 직접 사용하는 차트 라이브러리를 사용할 때, 컴포넌트에 필요한 데이터 GET,POST 등의 요청을 해서 받아올 때, 크로스 브라우징을 위해서 DOM 정보를 읽어올 때, 브라우저 스크롤 위치 등을 읽어올 때 사용할 수 있다

# 2. 컴포넌트 업데이트 시(props의 변화, state의 변화에 따라 컴포넌트가 업데이트되는 로직이 필요할 때)
> UNSAFE_componentWillReveiveProps(nextProps){
> if(this.props.myValue !== nextProps.myValue){
> // 원하는 function blah blah!
> }
> } 
  컴포넌트가 새로운 props를 받을 때 호출됨. 
  state가 props에 따라서 변해야되는 로직을 쓸 때 주로 씀. 
  this.props는 아직 변하지 않았고, nextProp가 새로 받게 될 props 이며, 
  둘을 비교해서 값이 다르면 호출되도록 로직을 짜면 됨. 
  매우 자주 쓰이는 API이지만 deprecated 됬음. getDerivedStateFromProps로 대
  체 되었다. 
> static getDerivedStateFromProps(nextProps,prevProps){
> } 
 - componentWillReceiveProps와의 차이점은 정적 메소드로 사용한다는 점이다. 
   정적 메소드는 클래스 내에만 존재하고 인스턴스에는 존재하지 않는 함수이다. 
  특정 props가 바뀔 때 설정하려는 state를 객체형태로 리턴하는 방식로 사용하면 된다.
  
> shouldComponentUpdate(nextProps,nextState){
>  // true를 리턴하면 getSnapshotBeforeUpdate 메소드가 실행된다. 
>  // props나 state를 업데이트하는 조건에 대한 로직을 작성하고 이를 true/false로 리턴하면 된다. 
> } 
 props, state 값이 변하기 직전에 호출 된다. next가 들어간 파라미터를 받는 메소드들은 전부 값 변화 직전에 호출된다고 보면 된다. 반면에 prev가 들어간 파라미터를 받는 다면 바뀐 값을 받는 거니까 값 변화 이후에 호출되는 거구나! 라고 이해하면 된다.
 리액트는 virtual DOM과 DOM을 비교해서 변화가 일어나는 부분만 업데이트 해준다. DOM 업데이트 전에 virtual DOM에 한번 렌더링 뒤에 업데이트 하게 되는데 virtual DOM 마저 불필요하게 렌더링되는 것을 막아서 성능 최적화를 돕는 API이다. 
 사용법을 예를 들자면
이 메소드에 return this.props.value !== nextProps.value 라는 한 줄이 들어가 있을 때 nextProps.value의 값이 달라져서 true를 리턴하게 되면 props를 업데이트 하고 리턴 값은 다시 false가 되므로 업데이트가 되지 않는 로직이다.
> UNSAFE_componentWillUpdate(nextProps, nextState) {
> }
  shouldComponentUpdate와 짝짜꿍 친구다. 친구가 true를 리턴할 때 호출된다. 
  이 API가 값을 리턴한 직후에 render()가 호출된다. 
  주로 컴포넌트 CSS 애니메이션 효과를 초기화하거나, 이벤트 리스너를 없앨 때 사용한다. 
  이번 16.3 패치로 deprecated 되었고 getSnapshotBeforeUpdate()로 대체할 수    있다. 
> getSnapshotBeforeUpdate(prevProps, prevState){
> }
 render() 이후 메소드 실행 순서: render() => getSnapshotBeforeUpdate() =>  DOM 변화 => componentDidUpdate()
 데이터베이스 관리에 있어서 snapshot이라는 말은 서로 관계된 table간의 특정 시점의 데이터를 순간포착해두는 것인데 이 용어와 일맥상통하는 것 같다. 
이 메소드도 prev 파라미터를 받는다. 즉 DOM 변화 직전의 실제 DOM 정보를 가져오게 된다. 이 메소드의 리턴값은 componentDidUpdate에서 3번째 parameter로 받아오게 된다. 
> componentDidUpdate(prevProps, prevState, snapshot){
> }
 이 메소드가 실행되는 시점에서 prop와 state가 바뀌었다. (prev prefix가 들어간 파라미터를 받고 있음)
snapshot 파라미터를 이용해서 이전 prevProps와 prevState 값을 받아와 이용할 수 도 있다.

# 3. 컴포넌트 제거 시
> componentWillUnmount(){
> }
 컴포넌트에 등록되있는 이벤트를 제거하는 로직이 들어간다. 그 외에도 외부 라이브러리를 이용해서 컴포넌트가 실시간으로 값을 받아오고 있다면 여기서 인스턴스를 제거해주는 로직을 작성하면 된다. 

# 4. 앱 crash 방지용 API
> componentDidCatch(error, info){
> }
 render()함수에서 오류가 발생하면 development 모드에서 빨간 오류창이 뜨지만 production 모드에서는 아예 하얀 창만 떠버린다. 이를 막기 위해서 따로 오류 발생 시 state를 오류모드로 바꾸는 로직을 만들면 오류 state에서 렌더링되는 컴퓨넌트를 보여주는 식으로 로직을 짤 수 있다. props 타입 관련 오류 실수를 막아줄 수 있을 것 같지만 만약 proptypes 와 함께 타입스크립트를 쓴다면 자주 사용할 것 같지는 않은 메소드이다. 
 > 예시 : https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html 

