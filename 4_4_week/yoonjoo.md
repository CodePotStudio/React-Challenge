## 이번 주에 한 일
이번주는 노마드코더 강의 중에서 트위터 클론코딩이라는 강의를 들으면서 firebase를 어떻게 사용하는지, 그걸로 로그인 기능과 데이터베이스를 어떻게 사용할 수 있는지 배웠습니다. (https://nomadcoders.co/nwitter/lobby) 그리고 이 기능들을 이용해서 개인적으로 가계부 프로그램을 만들어보려고 프로젝트를 시작했어요. 이번에 배운 거는 한국어를 못하는 친구에게도 보여주려고 영어로 작성했는데, 다시 한국어로 쓰는 게 의미가 없을 것 같아서... 이번 주 공부하면서 정리한 내용을 여기에 복붙합니다.

## set up firebase
After making a React project, install firebase writing
```
npm install --save firebase
```

Make a .env file and add the information there. 
```
REACT_APP_API_KEY =
REACT_APP_AUTH_DOMAIN =
REACT_APP_PROJECT_ID =
REACT_APP_STORAGE_BUCKET =
REACT_APP_MESSAGIN_ID =
REACT_APP_APP_ID =
```

Make a file in src `fbase.js`, and copy and paste the scripts for Firebase configuration. 
```
import firebase from "firebase/app";

const firebaseConfig = {
    apiKey: process.env.REACT_APP_API_KEY,
    authDomain: process.env.REACT_APP_AUTH_DOMAIN,
    projectId: process.env.REACT_APP_PROJECT_ID,
    storageBucket: process.env.REACT_APP_STORAGE_BUCKET,
    messagingSenderId: process.env.REACT_APP_MESSAGIN_ID,
    appId: process.env.REACT_APP_APP_ID
  };

export default  firebase.initializeApp(firebaseConfig);
```

In index.js, add
```
import fbase from "fbase";
```

and add '.env' in gitignore.

## set up router
Make a 'components' folder in src and make Router.js there.
Put the other pages in routes folder.

Install 
```
npm i react-router-dom
```

Ok, first change App.js in components so that it calls router.

```
import React, {useState} from 'react';
import AppRouter from 'components/Router';
import fbase from "fbase";

function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  return <AppRouter  isLoggedIn={isLoggedIn}/>;
}

export default App;
```

And my router looks like this:

```
import React from "react";
import {HashRouter as Router, Route, Switch }from "react-router-dom";
import Auth from "routes/Auth";
import Home from "routes/Home";

const AppRouter =  ({isLoggedIn}) => {    
    return (
        <Router>
        <Switch>
            {isLoggedIn ? (
            <>
            <Route exact path = "/">
                <Home />
            </Route>
            </>) : (<Route exact path = "/">
                <Auth />
                </Route>)}
        </Switch>
    </Router>
    )
}

export default AppRouter;
```

useState-hook is used here to check if the user is logged in or not. the default value is false, dvs 'not logged in'. 
This 'AppRouter' returns:
```
return (<Router><Switch> {isLoggIn ? show home : show login page} </Switch> </Router>)
```

Later, I wanted to add a navigation bar, so I added navigation.js in components.
```
import React from 'react';
import {Link} from 'react-router-dom';

const Navigation = () => (
    <nav> 
        <ul>
            <li>
                <Link to="/">Home</Link>
            </li>
            <li>
                <Link to="/profile">My Profile</Link>
            </li>
        </ul>
    </nav>
)

export default Navigation;
```

We can use `Link` so that the router checks the url and redirect to the page.
Changed Router like this:
```
const AppRouter =  ({isLoggedIn}) => {    
    return (
        <Router>
            {isLoggedIn && <Navigation />}
        <Switch>
            ...
        </Switch>
    </Router>
    )
}
```
It means, 'to show the Navigation, isLoggedIn should be true. 

## set up authentication

fbase.js before: 
```
import firebase from "firebase/app";

const firebaseConfig = {
    apiKey: process.env.REACT_APP_API_KEY,
    authDomain: process.env.REACT_APP_AUTH_DOMAIN,
    projectId: process.env.REACT_APP_PROJECT_ID,
    storageBucket: process.env.REACT_APP_STORAGE_BUCKET,
    messagingSenderId: process.env.REACT_APP_MESSAGIN_ID,
    appId: process.env.REACT_APP_APP_ID
  };

export firebase.initializeApp(firebaseConfig);

```

Now we want to use the functions for authentication, so we import it nad export the `firebase.auth()`.

```
import firebase from "firebase/app";
import "firebase/auth";

const firebaseConfig = {
    apiKey: process.env.REACT_APP_API_KEY,
    authDomain: process.env.REACT_APP_AUTH_DOMAIN,
    projectId: process.env.REACT_APP_PROJECT_ID,
    storageBucket: process.env.REACT_APP_STORAGE_BUCKET,
    messagingSenderId: process.env.REACT_APP_MESSAGIN_ID,
    appId: process.env.REACT_APP_APP_ID
  };

firebase.initializeApp(firebaseConfig);

export const authService = firebase.auth();
```

After changing this, fbase.js will export `authService`. 
Therefore, we change App.js so that it will use this authService.

```
import React, {useState} from 'react';
import AppRouter from 'components/Router';
import {authService} from "fbase";

function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(authService.currentUser);
  return <AppRouter  isLoggedIn={isLoggedIn}/>;
}

export default App;
```
Now the useState makes initial state with `authService.currentUser`.
This will check the currentUser, and if there is no user it will show the login page.

We add a log-in form in Auth.js, the login-page.

```
import React, {useState} from "react";

const Auth = () => {
    const [email, setEmail] = useState("");
    const [password, setPassword] = useState("");
    const onChange = (event) => {
        const {target: {name, value}} = event;
        if(name === "email") {
            setEmail(value)
        } else if(name ==="password"){
            setPassword(value)
        }
    }
    const onSubmit = (event) => {
        event.preventDefault();
    }
    return (
    <div>
    <form onSubmit = {onSubmit}>
        <input 
            name = "email"
            type="text" 
            placeholder ="Email" 
            required 
            value={email}
            onChange ={onChange}/>
        <input 
            name="password"
            type="password" 
            placeholder="Password" 
            required 
            value={password}
            onChange={onChange}/>
        <input type="submit" value="Log in" />
    </form>
    <div>
        <button>Continue with Google</button>
        <button>Continue with Github</button>
    </div>

    </div>);
}

export default Auth;
```

Here, we make two states, email and password, using hook. 
And then, we make a function for onChange, and one for onSubmit. The reason we write `event.preventDefault()` is to prevent refresing the page when we click onSubmit. 

The methods for login can be chosen here: https://console.firebase.google.com/u/0/project/nwitter-6f915/authentication/providers

To set email-login, we follow the instruction: https://firebase.google.com/docs/reference/js/firebase.auth.EmailAuthProvider

We have already imported authService, so just add `authService.createUserWithEmailAndPassword(email, password)` or `authService.signInWithEmailAndPassword(email, password);` to create or sign in. This is a 'promise', therefore we need to write async/await as well. 

This will happen when we submit the form, so let's change onSubmit. 
```
const onSubmit = async(event) => {
        event.preventDefault();
        try {
        let data;
        if(newAccount) {
            //create account
            await authService.createUserWithEmailAndPassword(email, password)
            //because this returns a promise, so we need to use async/await.
        }else{
            //login
            await authService.signInWithEmailAndPassword(email, password);
        }
    } catch (e) {
        console.log(e.message)
    }
}
```

Some people will want to log in, some people will want to create new account.
To change the mode, we can add new state, 'newAccount', which shows the mode. Using setNewAccount, we can toggle the mode.

```
const [newAccount, setNewAccount] = useState(true);
...
const toggleAccount = () => setNewAccount((prev) => !prev);
```

Lastly, if we get an error, for example there is already an account with same email address, or wrong password, we want to know/show the error message. So it is good to save the error message and show. We use the useState again.

```
const [error, setError] = useState("");
...
const onSubmit = async(event) => {
       ...
    } catch (e) {
        setError(e.message)
    }
}

```
And then we can `{error}` somewhere in html so that the error message is shown. 


App.js needs to be changed because it probably takes some time to bring the user information. 

```
function App() {
  const [init, setInit] = useState(false);
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  //add an observer detecting user
  useEffect(() => {
    authService.onAuthStateChanged((user) => {
      if(user) {
        setIsLoggedIn(true);
      }else{
        setIsLoggedIn(false);
      }
      setInit(true);
    });
  }, [])
  return (
    <>
    {init ? <AppRouter  isLoggedIn={isLoggedIn}/> : "Initializing..."}    
    </>
  );
}

export default App;
```

First, we create a init-state. After rendering, useEffect executes onAuthStateChanged (https://firebase.google.com/docs/reference/js/firebase.auth.Auth#onauthstatechanged). This is kind of 'observer', watching if someone is logging in/out. After detecting user/no-user, it changes the state 'isLoggedIn', and change even init to true. It means, init will be false until onAuthSateChange is done, and the router will be called after init becomes true. 


Now, socialLogin. 
https://firebase.google.com/docs/reference/js/firebase.auth.Auth#signinwithpopup
It says that we get 'provider' using `firebase.auth.GoogleAuthProvider()` and then, we can use the provider to login, like `auth.signInWithPopup(provider)`. Because this returns Promise <UserCredential>, we use async/await. 
```
const onSocialClick = async(event) => {
        const {target: {name}} = event;
        let provider;
        if(name === "google"){
            provider = new firebaseInstance.auth.GoogleAuthProvider();
        }else if(name === "github"){
            provider = new firebaseInstance.auth.GithubAuthProvider();
        }
        await authService.signInWithPopup(provider);
}
```
And now we can add this in the buttons for social-login. 

Added logout button in Profile.js, 
```
const Profile = () => {
    const onLogOutClick = () => authService.signOut();
    return (<>
        <button onClick = {onLogOutClick}>Log out</button>
        </>)
}
```
It works, but after logging out it does not redirect page. For this, we can add `<Redirect from ="*" to "/" />` after `<Route ...> </Route>`, or a hook `useHistory from 'react-router-dom'`.

```
const Profile = () => {
    const history = useHistory();
    const onLogOutClick = () => {
        authService.signOut();
        history.push("/");
    }
    return (<>
        <button onClick = {onLogOutClick}>Log out</button>
        </>)
}
```

## set up database
Add `import "firebase/firestore";` and `export const dbService = firebase.firestore();` in fbase.js, and make a database in Firebase.

Right now, we have a form to 'tweet' in Home.js,
```
import React, {useState} from "react";
import {dbService} from 'fbase';

const Home =  () => {
    const [nweet, setNweet] = useState("");
    const onSubmit = async(event) => {
        event.preventDefault();
        await dbService.collection("nweets").add({
            text: nweet,
            createdAt: Date.now()
        })
        setNweet("");
    }
    const onChange = (event) => {
        const {target: {value}} = event;
        setNweet(value);
    }

    return (
        <div>
            <form onSubmit = {onSubmit}>
                <input 
                    type="text" 
                    value= {nweet} 
                    onChange={onChange}
                    placeholder="What's in your mind?" 
                    maxLength={120} />
                <input 
                type="submit" 
                value="Nweet" />
            </form>
        </div>
    )
}
export default Home;
```
According to https://firebase.google.com/docs/reference/js/firebase.firestore.CollectionReference, `firebase.firestore.CollectionReference` has add(data) which returns a Promise. To use this, we make a state 'nweet', and when we write something in the form 'setNweet' will change the state. When we submit the form, we will add the 'nweet' in our collection "nweets" with time. Finally, we reset the state using setNweet so that the form shows a blank text field. 

To read data from the database, we can add another state, `nweets`. `get()` returns `QuerySnapshot` and it has `forEach` which calls for each document in the snapshot. Using this, we can print out document.data(), as below. 

```
const Home =  () => {
    const [nweet, setNweet] = useState("");
    const [nweets, setNweets] = useState([]);
    const getNweets = async() => {
        const dbNweets = await dbService.collection("nweets").get();
        dbNweets.forEach((document) => {
            const nweetObject = {
                ...document.data(),
                id: document.id
            }
            setNweets(prev => [nweetObject, ...prev]);
        });
    }
    useEffect(()=>{
        getNweets();
    }, [])

    ...
    return (
        <div>
            <form onSubmit = {onSubmit}>
             ...
            </form>
            <div>
                {nweets.map(nweet => <div key = {nweet.id}>
                    <h4>{nweet.nweet}</h4>
                </div>)}
            </div>
        </div>
    )
}
```
Using forEach, we make nweetObject for every document from the database. The nweetObject contains nweet and time (as we added a tweet in the database before), and even id. setNweets will update the state, merging the new data with previous one. All of these things will happen after init-rendering, so we use useEffect.
