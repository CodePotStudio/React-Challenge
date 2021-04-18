---
title: useImperativeHandle
author: mingyu.gu
date: 2021.04.18
---

# useImperativeHandle

> `useImperativeHandle`은 `ref`를 사용할 때 부모 컴포넌트에 노출되는 인스턴스 값을 사용자화합니다. `forwordRef`와 함께 사용하세요.
> 
> [kr.reactjs.org](https://ko.reactjs.org/docs/hooks-reference.html#useimperativehandle)

매우 간단하게 소개되어 있는 내용이지만 이번 프로젝트를 하면서 사용할 기회가 생겨, 어떻게 사용했는지 알려드리려 합니다.

## Use case
회원가입 Form을 만들 때 주민등록번호를 입력해야하는 input을 만들어야하고 해당 input의 value는 `ref`를 통해 접근한다고 해봅시다.

```tsx
// SignUpForm.tsx
const SignUpForm: React.FC = () => {
  const nameRef = useRef<HTMLInputElement>(null);
  const regisdentNumberRef = useRef<HTMLInputElement>(null);

  return (
    <form>
      <InputField name="name" placeholder="이름" ref={nameRef} />
      <ResidentNumberField name="residentNumber" ref={regisdentNumberRef} >
    </form>
  )
}
```
이름의 경우 `input` 이 하나만 필요해서 상관은 없지만 `ResidentNumberField`의 경우에는 `input`이 두개 들어가게 됩니다. 이때 value를 어떻게 전달할 수 있을지 고민하다 `useImperativeHandle`을 사용하면 해결할 수 있을꺼라 생각했습니다.

```tsx
interface Ref {
  type: string;
  name: string;
  value: string;
}

interface ResidentNumberFieldProps extends React.InputHTMLAttributes<HTMLInputElement> {
  className?: string;
}

const ResidentNumberField = React.forwardRef<Ref, ResidentNumberFieldProps>(props, ref) => {
  const firstInputRef = useRef<HTMLInputElement>(null);
  const secondInputRef = useRef<HTMLInputElement>(null);

  useImperativeHandle(ref, () => ({
    type: 'text',
    name: props.name ?? 'residentNumber,
    get value() {
      return `${firstInputRef.current?.value ?? ''}${
        secondInputRef.current?.value ?? ''
      }`;
    }
  }), []);

  return (
    <>
      <FirstInputField
        {...props}
        type="number"
        placeholder="주민번호 앞자리"
        ref={firstInputRef}
      />
      <SecondInputField
        {...props}
        type="number"
        placeholder="주민번호 뒷자리"
        ref={firstInputRef}
      />
    </>
  )
}
```
`useImperativeHandle`를 이용하여 두개의 input을 하나의 input처럼 사용할 수 있었습니다.

## 마무리
이러한 방법외에 value값을 임의로 조작하던가, 여러 속성들을 customize 할 수 있는 등 `useImperativeHandle`를 적절하게 사용하면 매우 유용한 Hooks이라는 것을 알 수 있었습니다.
