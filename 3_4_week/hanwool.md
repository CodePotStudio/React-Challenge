# React에서 외부 스크립트 불러오기

React를 사용하다 보면 npm 이나 yarn과 같은 패키지 매니저를 통해서 외부 라이브러리를 설치할 수 도 있지만, 외부에서 작성된 script 파일을 불러와 사용해야 할 때가 있습니다.

이번 포스팅에서는 React에서 외부 script를 효과적으로 불러오는 방법을 알아보도록 하겠습니다.

## React Helmet 사용하기

React Helmet은 html의 head 태그를 관리할 때 가장 많이 사용하는 라이브러리입니다.

### React Helmet 설치하기

```jsx
npm install --save react-helmet
```

### Helmet을 통해 script 불러오기

Helmet을 통해서 script를 불러오는 방법은 아래와 같습니다.

```jsx
import { Helmet } from "react-helmet";

const App = () => (
	<div className="application">
		<Helmet>
			<script
				src="https://developers.somthingSDK.com/sdk/script.js"
				type="text/javascript"
			/>
		</Helmet>
	</div>
);
```

위와 같이 react-helmet에서 Helmet 컴포넌트를 import하고, 내부에 우리가 필요로 하는 <script> 태그를 추가해 주면 됩니다.

### Helmet으로 script를 불러올 때 생기는 문제

helmet은 head 태그를 관리하는 것에 초점이 맞추어 져있기 때문에, script의 로딩 상태를 확인 하는 기능이 없습니다. 예를 들어, 외부 결제 모듈을 sdk로 사용하면 sdk가 로딩이 완료되고, sdk를 초기화하는 과정이 필요한데, helmet을 사용할 경우 sdk가 로딩이 되지 않은 상태에서 초기화를 시도하여 문제가 발생할 수 있습니다.

## React custom hooks 사용하기

Helmet에서 script loading 상태를 확인하지 않아 생겼던 문제들을 custom hooks를 활용해서 해결해 보도록 하겠습니다. `React`에서 `html`에 `script`를 넣는 hooks를 직접 구현할 수 있지만, 이 것 또한 많은 사람들이 고민해 놓은 과제이므로 [usehooks](https://usehooks.com/useScript/) 사이트에서 다른 사람들이 만들어 놓은 `hooks`를 사용해 보도록 하겠습니다.

### useScript hooks

`src` 아래 `hooks.js` 파일을 하나 만들고, 아래 코드를 붙여넣기 해줍니다.

```jsx
// hooks.js
import { useState, useEffect } from "react";

function useScript(src) {
	// Keep track of script status ("idle", "loading", "ready", "error")
	const [status, setStatus] = useState(src ? "loading" : "idle");

	useEffect(
		() => {
			// Allow falsy src value if waiting on other data needed for
			// constructing the script URL passed to this hook.
			if (!src) {
				setStatus("idle");
				return;
			}

			// Fetch existing script element by src
			// It may have been added by another intance of this hook
			let script = document.querySelector(`script[src="${src}"]`);

			if (!script) {
				// Create script
				script = document.createElement("script");
				script.src = src;
				script.async = true;
				script.setAttribute("data-status", "loading");
				// Add script to document body
				document.body.appendChild(script);

				// Store status in attribute on script
				// This can be read by other instances of this hook
				const setAttributeFromEvent = (event) => {
					script.setAttribute(
						"data-status",
						event.type === "load" ? "ready" : "error"
					);
				};

				script.addEventListener("load", setAttributeFromEvent);
				script.addEventListener("error", setAttributeFromEvent);
			} else {
				// Grab existing script status from attribute and set to state.
				setStatus(script.getAttribute("data-status"));
			}

			// Script event handler to update status in state
			// Note: Even if the script already exists we still need to add
			// event handlers to update the state for *this* hook instance.
			const setStateFromEvent = (event) => {
				setStatus(event.type === "load" ? "ready" : "error");
			};

			// Add event listeners
			script.addEventListener("load", setStateFromEvent);
			script.addEventListener("error", setStateFromEvent);

			// Remove event listeners on cleanup
			return () => {
				if (script) {
					script.removeEventListener("load", setStateFromEvent);
					script.removeEventListener("error", setStateFromEvent);
				}
			};
		},
		[src] // Only re-run effect if script src changes
	);

	return status;
}

export { useScript };
```

`useScript`의 기능은 `src`에 해당하는 `script`를 불러오고, `script`의 상태를 결과 값으로 내려주는 `hooks`입니다. 이렇게 사용하는 이유는 `script`를 비동기로 불러오기 때문에 `script`가 다 불러왔을 때 우리가 원하는 작업들을 하기 위함입니다.

### hooks 사용하기

```jsx
import { useScript } from "hooks";

const App = () => {
	const status = useScript("https://developers.somthingSDK.com/sdk/script.js");
	useEffect(() => {
		if (status === "ready") {
			// sdk 초기화하기
			window.SomeThingSDK();
		}
	});
};
```

우선 useScript를 통해서 script를 불러옵니다. 그리고 useEffect 안에서 useScript가 return한 script의 loading 상태를 확인하고, loading 상태에 따라 SDK를 초기화하는 코드를 추가합니다.

이를 통해 sdk가 loading이 완료된 이후에 sdk를 초기화를 할 수 있게 되었습니다.
