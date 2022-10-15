
# Create React-Firebase Project with Authentication

A brief description of how to create a react Firebase project with Firebase Authentication.


## Procedure

#### Here we'll create Authentication following this [tutorial](https://youtu.be/LCAXfjb0oYo)

 1. Goto this url [console.firebase.google.com](https://console.firebase.google.com/)
 2. Create a new project
 3. Now select `Authentication` 
 from left panel and from `Sign-in method` tab select 
 `Email/Password`

 4. After that, select `Firestore Database` from left panel 
 and create a database (Production/Test mode).

 5. From `Project Settings` add a Web App. After adding 
 app we'll get a configuration. Inside `src>lib>init-firebase.js`
 add these configuration code.

***init-firebase.js***
```bash
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: "AIzaSyDVCFNDhnqiy-viEO6C_MrpBXoHftFnVQw",
  authDomain: "react-firebase-c2319.firebaseapp.com",
  projectId: "react-firebase-c2319",
  storageBucket: "react-firebase-c2319.appspot.com",
  messagingSenderId: "440229316033",
  appId: "1:440229316033:web:ab7170bfe7b331f3399b40",
  measurementId: "G-V1QPMHNXH2"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
//const analytics = getAnalytics(app);
```

6. Now install the following packages for firebase and routing

```bash
npm install firebase
npm install react-router-dom
```
To manage Authentication state of the user install this package

```bash
npm install react-firebase-hooks
```

7. import getAuth from firebase in ***init-firebase.js***

```bash
import {getAuth} from 'firebase/auth'
....
const auth=getAuth(app);
export {auth}
```
8. Now create ***SignIn.jsx*** and ***Home.jsx*** pages with 
following codes. Here if successfully signed In from SignIn
page then it'll redirect to the home page. Then user can logout from home page.


***SignIn.jsx***
```bash
import React, { useState } from 'react'
import {signInWithEmailAndPassword,createUserWithEmailAndPassword} from 'firebase/auth'
import { auth } from '../lib/init-firebase'
import { useNavigate } from 'react-router-dom'

const SignIn = () => {
    const navigate=useNavigate()
    const [email,setEmail]=useState('')
    const [password,setPassword]=useState('')

    const signIn=()=>{
        signInWithEmailAndPassword(auth,email,password)
            .then(auth => {navigate('/home')})
            .catch(error=>console.error(error))
    }
    const register=()=>{
        createUserWithEmailAndPassword(auth,email,password)
            .then(auth=> {navigate('/home')})
            .catch(error=>console.error(error))
    }
  return (
    <div>
        <h1>Sign in </h1>
        <label>Email</label>
        <input className='email' onChange={e=>setEmail(e.target.value)}/>
        <label>Password</label>
        <input className='password' onChange={e=>setPassword(e.target.value)}/>
        <button onClick={signIn}>Sign In</button>
        <button onClick={register}>Register</button>
    </div>
  )
}

export default SignIn
```

***Home.jsx***
```bash
import React from 'react'
import {useAuthState} from 'react-firebase-hooks/auth'
import { auth } from '../lib/init-firebase'
import { useNavigate } from 'react-router-dom'

const Home = () => {
    const navigate=useNavigate()
    const [user,loading,error]=useAuthState(auth)
    console.log(user)
  return (
    <div>
        <h1>
            welcome {user?.email}
        </h1> 
        <button onClick={()=>{auth.signOut(); navigate('/')}}>
            SignOut
        </button>
    </div>
  )
}

export default Home
```
9. Now add routing code in ***App.js***
```bash
import './App.css';
import Home from './pages/Home';
import SignIn from './pages/SignIn'
import { BrowserRouter, Routes, Route } from "react-router-dom";

function App() {
  return (
    <div className="App">
      <BrowserRouter>
      <Routes>
        <Route path="/home" element={<Home/>}/>
        <Route path="/" element={<SignIn />} />
      </Routes>
    </BrowserRouter>
    </div>
  );
}

export default App;

```