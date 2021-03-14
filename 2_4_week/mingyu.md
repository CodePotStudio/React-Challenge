---
title: React Error Boundaries
author: mingyu.gu
---

# React Error Boundaries

## React.lazy & Suspence

React 비동기 렌더링을 위해서 React.lazy 와 Suspence를 사용하곤 합니다.

```jsx
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

## Error Boundaries

Suspense의 fallback의 경우 React.lazy로 인한 컴포넌트가 로드되기 전까지 표시가 됩니다. 하지만 네트워크 장애 같은 이유로 모듈 로드에 실패할 경우 에러가 발생할 수 있는데, 이때 사용하는 것이 Error Boundaries 입니다. 

```jsx
import React, { Suspense } from 'react';
import MyErrorBoundary from './MyErrorBoundary';

const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

const MyComponent = () => (
  <div>
    <MyErrorBoundary>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </MyErrorBoundary>
  </div>
);
```

UI의 일부분에 존재하는 자바스크립트 에러가 전체 애플리케이션을 중단시켜서는 안되기에 React 16에서는 에러 경계("error boundary")라는 개념이 도입되었습니다.

에러 경계는 **하위 컴포넌트 트리의 어디에서든 자바스크립트 에러를 기록하며 깨진 컴포넌트트리 대신 폴백 UI를 보여주는** React 컴포넌트입니다. 에러 경계는 렌더링 도중 생명주기 메서드 및 그 아래에 있는 전체 트리에서 에러를 잡아냅니다.

Lifecycle method인 `static getDerivedStateFromError()` 와 `componentDidCatch()` 중 하나를 정의하면 클래스 컴포넌트 자체가 에러 경계가 됩니다. 에러가 발생한 뒤에 fallback UI를 렌더링하려면 `static getDrivedStateFromError()`를 사용하고, 에러 정보를 기록하려면 `componentDidCatch()`를 사용하면 됩니다.

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 다음 렌더링에서 폴백 UI가 보이도록 상태를 업데이트 합니다.
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 에러 리포팅 서비스에 에러를 기록할 수도 있습니다.
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 폴백 UI를 커스텀하여 렌더링할 수 있습니다.
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

그런 다음엔 아래와 같이 일반 컴포넌트로 사용할 수 있습니다.

```jsx
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```
