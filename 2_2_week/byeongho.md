# React Hooks

### React component의 종류

1. class component
- 더 많은 기능을 제공 하지만 느려 진다.

 2. functional component

- 구현은 간단하지만, 한정적 기능 , 빠른 성능.
- state 지정 못함.
- life cycle method사용 못함.

- React Hooks : react 16.8부터 functional component에서도 , class component의 기능을 구현 할 수 있다.

### Hooks가 생기게 된 이유

1. 컴포넌트 사이에서 상태와 관련된 로직을 재사용하기 어려움

→ Hook은 계층 변화 없이 상태 관련 로직을 재사용할 수 있도록 도와줌

 2. 복잡한 컴포넌트들은 이해하기 어려움

→ Hook을 통해 로직에 기반을 둔 작은 함수로 컴포넌트를 나눌 수 있음. (구독 설정 및 데이터를 불러오는 것과 같은 로직) 

 3. Class은 사람과 기계를 혼동야기

→ Hook은 Class없이 React 기능들을 사용할 수 있음.

### React Hooks Form 을 이용한 유효성 체크

1. 설치

```jsx
npx create-react-app ./
npm install react-hook-form --save
```

2. 예제 파일 다운 받기

[https://react-hook-form.com/get-started](https://react-hook-form.com/get-started)

3. Example 파일 분석

```jsx
import React from "react";
import { useForm } from "react-hook-form";

export default function App() {
  const { register, handleSubmit, watch, errors } = useForm();
  const onSubmit = data => console.log(data);

  console.log(watch("example")); // Example이라는 name의 element를 관찰

  return (
    {/* "handleSubmit" will validate your inputs before invoking "onSubmit" */}
    <form onSubmit={handleSubmit(onSubmit)}>
    
      <input name="example" defaultValue="test" ref={register} />
      //관찰할때는 register로 '등록'을 해줘야 한다.
      
      <input name="exampleRequired" ref={register({ required: true })} />
			//어떠한 식으로 유효성 체크를 할 지 조건을 넣어 준다.

      
      {errors.exampleRequired && <span>This field is required</span>}
			//유효성 체크에 걸렸을때 에러 문구를 설정해 준다.      

      <input type="submit" />
    </form>
  );
}
```

4. 회원가입 Form 만들기

```jsx
import './App.css';
import { useForm } from 'react-hook-form'
import React, {useRef} from 'react'

function App() {

  const { register , watch , errors, handleSubmit} = useForm();
  const password = useRef();
  password.current = watch("password");
  console.log(watch('email'));

  const onSubmit = (data) => {
    console.log('data' , data)
  }

  //data => 이메일 , 이름 , 비밀번호

  return (
   
   <form 
   onSubmit={handleSubmit(onSubmit)}
   >
  
     <label>Email</label>
     <input name="email" type="email" 
     ref={register({
        required: true ,
         pattern : /^\S+@\S+$/i
         })} />

    {errors.email && <p>유효 하지 않는 이메일 입니다</p>}
     
     
     <label>Name</label>
     <input name="name" 
     ref={register({ 
       required: true,
        maxLength: 10
       })} />

    {errors.name && errors.name.type === "required" && <p>이름은 필수 입력 입니다.</p>}
    {errors.name && errors.name.type === "maxLength" && <p>이름은 10자리 미만 입니다.</p>}

    <label>Password</label>
    <input 
          name="password" 
          type="password"
          ref={register({ 
            required: true,
             minLength: 6
          })} />
    {errors.password && errors.password.type === "required" && <p>비밀번호는 필수 입력 입니다.</p>}
    {errors.password && errors.password.type === "minLength" && <p>비밀번호는 6자리 이상 입니다.</p>}

    <label label>Password Confirm</label>
     <input 
          name="password_confirm" 
          type="password"
          ref={register({ 
            required: true,
             minLength: 6,
             validate: (value) =>
             value === password.current
          })}
     />
    
    {errors.password_confirm && errors.password_confirm.type === "required" && <p>비밀번호는 필수 입력 입니다.</p>}
    {errors.password_confirm && errors.password_confirm.type === "minLength" && <p>비밀번호는 6자리 이상 입니다.</p>}
    {errors.password_confirm && errors.password_confirm.type === "validate" && <p>비밀번호가 일치 하지 않습니다.</p>}
    
     
     <input type="submit" />
   </form>
  );
}

export default App;
```

![React%20Hooks%208ee35380057d4a04821639f32069acab/_2021-02-28__1.36.56.png](React%20Hooks%208ee35380057d4a04821639f32069acab/_2021-02-28__1.36.56.png)

![React%20Hooks%208ee35380057d4a04821639f32069acab/_2021-02-28__1.52.10.png](React%20Hooks%208ee35380057d4a04821639f32069acab/_2021-02-28__1.52.10.png)

### UseRef?

→ onChange가 없어도 react-hook-form 모듈을 이용해서 각 값을 가져 올 수 있다.

→ useRef란? 

특정 DOM을 선택할때 사용 한다.

보통 JS ⇒ getElementById , querySelector 같은 DOM Selector 함수를 사용해서 DOM을 성택

→ 리액트에서는 useRef를 사용 한다.

### React-hook-from 사용하지 않았을때의 로직,

Input에 onChagnge= {handleChage} 함수를 만들어서

const [name,setName] = useState("") name이라는 state를 만들어서

const handleChagne = (e) ⇒ {

setName(e.target.value) //업데이트 처리함

}

Input에 value로 들어가게 됨.

1. Class Component → React.createRef 

```jsx
class Mycomponent extends React.component {
	constructor(props) {
		super(props);
		this.myRef = React.createRef();
}
	render() {
		return <div ref={this.myRef} />;
	}
}
```

2. function Component → useRef

```jsx
class MyComponent() {
	const myRef = useRef(null);
	return (
		<div ref={myRef} />
	)
}
```

### DOM을 직접 선택 해야 할 경우들

1. 엘리먼트 크기를 가져와야 할때
2. 스크롤 바 위치를 가져와야 할떄
3. 엘리먼트(이름 ,이메일 텍스트필드) 에 포커스를 설정 해줘야 할때 등등...

### UseRef을 이용해서 어떻게 password와 password confirm이 같은지 알수 있는지?

1. ref 생성
2. watch를 이용하여 password필드 값을 가져오기
3. 가져온 password값을 ref.current에 넣어주기


참고
[https://ko.reactjs.org/docs/hooks-intro.html](https://ko.reactjs.org/docs/hooks-intro.html)
[https://www.youtube.com/watch?v=tWOn7g_3wKU&list=PL9a7QRYt5fqlnuhU_Zgj2jKu6Ldw6UUCW](https://www.youtube.com/watch?v=tWOn7g_3wKU&list=PL9a7QRYt5fqlnuhU_Zgj2jKu6Ldw6UUCW)