# Hooks API - useMemo (2)

# 조금 더 공부해보기

이전에 `useMemo` 에 대해 쓴 글에 같이 공부하시는 분들께서 조금 더 공부하면 좋은 것들을 적어주셔서 조금 더 공부해보기로 하였다. 

> `useMemo` 는 연산을 줄여주기에 무조건 좋다고 생각했는데, 알아보니 사용하지 말아야 할 때도 있다더군요! 그것도 한 번 알아보시면 좋을 것 같습니다 - OhJuhun 님

이건 정말 의외였다. 기존 값에 기반하여 새로운 값을 연산할 때, 기존 값을 사용하면 여러번 계산할 필요가 없다는 게 메모이제이션의 장점인데 사용하지 말아야 할 때도 있고 그걸 알아보면 좋겠다는 조언을 들었다. 

그래서 [when we don't use useMemo](https://www.google.com/search?q=when+we+don%27t+use+useMemo&rlz=1C5CHFA_enKR928KR928&oq=when+we+don%27t+use+useMemo+&aqs=chrome..69i57.8383j1j7&sourceid=chrome&ie=UTF-8) 라는 검색어로 검색을 했더니 상당히 많은 글들이 나왔고 그 중 상단에 위치한 [You’re overusing useMemo: Rethinking Hooks memoization](https://blog.logrocket.com/rethinking-hooks-memoization/) 라는 글을 읽어보기로 했다. 아래에 적는 글은 해당 글을 읽으며 요약한 내용들이다.

---

# useMemo 를 너무 과하게 사용하진 않나요?: Hooks Memoization 에 대해 한 번 더 생각해보기

# TL;DR

1. `useMemo` 를 사용하는 해당 연산이 과연 진짜 고비용 연산인지 Big-O Notation 기준으로 생각해보자
2. `useMemo` 를 단지 `ESLint` 에서 나오는 경고문구를 피하기 위해서 사용하는 건 아닌지 생각해보자
3. `useMemo` 를 정말 연산횟수를 줄이기 위해 사용하는 게 아니라, 관계적 동등성(Referential Equalities)을 유지하기 위해서 사용하는 건 아닌지 생각해보자

만약 해당 값을 구하는 데 필요한 연산량이 많은 게 확실하고, ESLint 의 경고문구를 피하기 위해서 그냥 사용한 게 아니며, Referential Equality 를 위하여만 사용하는 것도 아니라면 그 때는 `useMemo` 를 쓰는게 맞지만, 1, 2, 3번에 해당된다면 더 나은 대안들을 찾아보자.

1번은 그냥 `useMemo` 를 쓰지 않는 것이 이득이고, 2번과 3번은 `useRef` 를 사용하는 게 더 이득이다.

최악을 일단 피하고 봐야겠다는 사고방식으로 `useMemo` 를 사용하는 게 결코 나쁘다는 건 아니지만, 그렇다고 차악을 용인할 필요는 없습니다. 최악을 피해 차악을 택하는 것도 좋지만, 차악마저도 피할 수 있다면 가급적이면 최선을 택하자.

---

# 1. 과연 useMemo 를 사용하는 연산이 정말 많은 계산을 요하는 연산이 맞는가?

```jsx
/** 
  @param {number} page 
  @param {string} type 
**/
const myComponent({page, type}) { 
  const resolvedValue = useMemo(() => {
     getResolvedValue(page, type)
  }, [page, type])

  return <ExpensiveComponent resolvedValue={resolvedValue}/> 
}
```

이 코드는 `useMemo` 를 통해서 `getResolvedValue` 라는 연산을 오직 `page` 라는 변수가 바뀌거나, `type` 라는 변수가 바뀌는 경우에만 처리한다. 만약 `getResolvedValue` 라는 연산이 많은 연산량을 요한다면 `useMemo` 를 사용하는 게 합리적이다. 왜냐면, 그럴 때 쓰라고 만든게 memoization 을 활용한 `useMemo` hook 이니깐.

그러나 그 연산이 Big-O 를 기준으로 판단했을 경우 그렇게 많이 부하가 걸리는 연산이 아니라면? 그 때는 그냥 내장된 메서드를 사용하여 해결하자. JS 의 `Array.prototype.map` 등의 자료형에 걸맞는 메서드들은 상당한 수준으로 최적화가 되어있다. 

이걸 통해서 충분히 처리할 수 있는 연산량임에도 불구하고 `useMemo` 를 사용한다면 배보다 배꼽이 더 커질 수 있다. `[useMemo`, `useCallBack` 을 언제 쓰면 좋을지에 대해서 설명한 글도 같이 읽어보면 좋다.](https://kentcdodds.com/blog/usememo-and-usecallback)

# 2. memoized value 의 참조값(reference value)이 변경되는가?

```jsx
const resolvedValue = useMemo(() => {
	getResolvedValue(page, type)
}, [page, type]);
```

위의 코드에서 `useMemo` 부분만 뜯어서 다시 한 번 보자. 예를 들어서 `page`  에 2가 들어가고, `type` 에 "GET" 이 들어간다면, `resolvedValue` 를 연산하기 위해 참조하는 값이 바뀌는가? 

간단하게 생각하면 된다. `resolvedValue` 의 data type 이 `primitive value` 인지 아닌지만 보면 된다. JS는 `string`, `number`, `boolean`, `null`, `undefined`, `symbol` 라는 원시값(primitive value) 를 가지고 있는데, 만약 useMemo 를 통해서 구하는 데 연산을 줄이려고 했던 값의 형태가 저 원시값 중 하나라면, `useMemo` 를 굳이 사용할 필요가 없다. 

왜냐면, component 가 다시 render 되는 상황을 피하기 위해 `useMemo` 를 사용하는데 참조값이 달라지지 않는다면 어차피 `useMemo` 를 사용하지 않아도 re-render 되지 않기 때문이다. 

```jsx
// if typeof resolvedValue is one of the primitive values of JS...
function MyComponent({page, type}) {
	const resolvedValue = getResolvedValue(page, type);
	return <ExpensiveComponent resolvedValue={resolvedValue}/>
};
```

만약 저 resolvedValue 가 `true`, `2`, `"resolved completed"` 등의 primitive value 라면, 그 값을 구하는 데 사용하는 연산인 `getResolvedValue` 는 고비용 연산이 아니다. 그 이유는 위에 말했듯 참조값이 달라지지 않기 때문이다. 그래서 그냥 위에 나온 것처럼 함수를 통해 연산한 값을 바로 할당해서 사용해도 무방하다.

# 3. 기본상태(default state)를 memoize 하는 이유들에 대해 생각해보기

```jsx
/** 
  @param {number} page 
  @param {string} type 
**/
function myComponent({page, type}) { 
  const defaultState = useMemo(() => ({
    fetched: someOperationValue(),
    type: type
  }), [type])

  const [state, setState] = useState(defaultState);
  return <ExpensiveComponent /> 
}
```

이 코드는 `useMemo` hook 을 통해서 `defaultState` 를 계산하고, `type` 이 변경될 때에만 해당 연산을 수행하도록 하였다. 충분히 목적이 드러나는 코드이긴 하다. `type` 이 변경되지 않는 한, `useState` 의 기본값으로 주어지는 parameter 인 `defaultState` 는 기본값 상태 그 자체를 유지하며, `type`  이 변경될 때에만 다시 연산을 수행하려는 목적의 코드임을 알 수 있다. 

그러나 이는 `useState` hook 을 제대로 이해하지 못 한, 잘못된 접근 방식이다. useState hook 에 주어지는 default parameter 는 특정 component 가 re-mount 될(`componentDidMount` 의 상황) 때에만 작동하지, component 가 단순히 re-render(`componentDidUpdate` 의 상황) 되는 상황에서 작동하지 않기 때문이다.

그렇기 때문에 위에 적은 코드는 잘못된 접근방식이라고 할 수 있다. 왜냐면 useState 는 `setState` 라는 함수를 통해 업데이트가 되고, 오직 그 경우에만 state 중 가장 최근에 갱신한 값을 불러올 뿐이고, 그렇기 때문에 그냥 default parameter 자리에 들어가는 `defaultParameter`  라는 object 를 변경하는 걸로는 작동하지 않기 때문이다. 

```jsx
function myComponent({page, type}) {
	const defaultState = () => {
		return {
			fetched: someOperationValue(),
			type: type,
	}

	const [state, setState] = useState(defaultState);
	return <myComponent />;
}
```

그래서 해당 상황은 `useMemo` 를 사용하는 게 아니라, 이런 식으로 `defaultState` 라는 함수를 선언한 뒤, defaultState 가 `useState` hook 을 통해 사용될 때 메모리에 할당되게끔 function expression 방식을 사용하여 필요할 때만 연산을 해주는 것이 더 합리적이라고 할 수 있다.

---

다음 단락인 "ESLint 에서 나오는 경고문구를 피하기 위한 useMemo 사용" 에 대해서는 다음 주에 마저 작성해봐야겠습니다. 다음 주에 해당 포스팅을 이해하고 하나의 포스팅으로 다시 만들어보겠습니다.