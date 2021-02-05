# Recoil

> A state management library for React

드디어 서드 파티가 아닌 리액트 자체 상태 관리 라이브러리가 나왔구나 라고 생각했지만, 아쉽게도 "리액트"팀이 아니라 페이스북 내 타 팀에서 만든 툴입니다. 하지만 여타 다른 상태관리 툴보다 우월한점은 리액트 내부 스케쥴러에 접근할 수 있다는 것이죠.

## Context API가 있잖아?

React에 내장된 상태 공유 API가 있긴합니다. 하지만 이는 locale/theme처럼 업데이트가 현저히 낮게 발생하는 상태를 공유할때만 효과적입니다. 몇가지 한계점을 보시죠.

- 상태 업데이트시 모든 DOM 트리가 re-rendering된다.
- 각 컨텍스트는 단일 값만 저장할 수있다.
- context의 leaf와 root의 코드 분리가 어렵다. 

## Core Concept

_Recoil의 데이터 흐름_

> **atom**(shared state) -> **component**   
> or   
> **atom**(shared state) -> **selector**(pure function) -> **component**   

Atom은 Component가 구독할 수 있는 상태의 단위입니다. Selector는 이 상태를 동기/비동기로 변환합니다.

### Atom

상태 단위로써 업데이트 및 구독이 가능합니다. atom이 업데이트되면 이를 구독하고 있는 Component들은 re-rendering됩니다.

```javascript
const fontSizeState = atom({
    key: 'fontSizeState',
    default: 14,
});
```

- key: atom은 고유 key가 필요합니다. 디버깅, persistency, 특정 고급 API를 위함이죠.
- default: 기본값 입니다.

이제 상태를 사용하기 위한 hook이 필요한데, 이를 `useRecoilState`라고 합니다.(이 훅을 통해 컴포넌트간 상태 공유가 가능합니다)

```javascript
function FontButton() {
    const [fontSize, setFontSize] = useRecoilState(fontSizeState);
    return (
        <button onClick={() => setFontSize(size => size + 1)}
                size={{ fontSize }}>
            확대하려면 클릭
        </button>
    );
}
```

버튼을 클릭하면 다른 컴포넌트에서도 증가된 폰트 사이즈를 사용할 수 있습니다.

```javascript
function Text() {
    const [fontSize, setFontSize] = useRecoilState(fontSizeState);
    return <p style={{ fontSize }}>이 텍스트 또한 사이즈가 증가합니다.</p>;
}
```

### Selector

순수 함수 이며, input으로 __atom__ 혹은 또다른 __selector__ 를 인자로 받습니다. 인자로 받은 atom/selector가 업데이트 되면 다시 평가됩니다. 이를 구독하고 있던 Component는 selector가 업데이트됨에 따라 re-rendering됩니다.

컴포넌트 입장에서 atom/selector는 동일한 인터페이스를 제공하므로 서로를 대체할 수 있습니다.

```javascript
const fontSizelabelState = selector({
    key: 'fontSizeLabelState',
    get: ({ get }) => {
        const fontSize = get(fontSizeState);
        const unit = 'px';

        return `${fontSize}${unit}`;
    },
})
```

`get` 속성은 계산이 필요한 함수입니다. 인자로 전달된 `get`함수를 통해 atom의 값에 젒근할 수 있습니다. 또는 selector에 접근할 수도 있죠. 자동으로 의존성 관계가 만들어지고 상위(upstream)의 atom/selector가 업데이트 되면 하위 atom/selector는 다시 계산됩니다. 

Selector는 `useRecoilValue()`를 사용해 읽을 수 있습니다.

```javascript
function FontButton() {
    const [fontSize, setFontSize] = useRecoilState(fontSizeState);
    const fontSizeLabel = useRecoilValue(fontSizeLabelState);

    return (
        <>
            <div>Current font size: {fontSizeLabel}</div>

            <button onClick={() => setFontSize(size => size + 1)}
                    size={{ fontSize }}>
                확대하려면 클릭
            </button>
        </>
    )
}
```

---

#### Reference

https://recoiljs.org