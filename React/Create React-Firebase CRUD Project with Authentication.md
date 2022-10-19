
# Create React-Firebase CRUD Project with Authentication

A brief description of how to create a react Firebase project with Firebase Authentication.


## Adding Firebase Authentication to React

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


## Adding CRUD to React with Firebase Backend

#### Here we'll add crud operation following this [tutorial](https://youtu.be/F7t-n5c7JsE).
We already setup the `Firestore Database` in Authentication steps and added firebase package.
Here we'll continue with further process.

1. In cloud Firestore database first give a collection Id/name `todos` then create 
`title` and `isCompleted` fields with some value in the document and provide a 
`document ID` or click auto generate.

2. Create another collection named `movies` give add `name` field with a value

3. Now import `getFirestore` and add the following code in firebase config file 
```bash
import {getFirestore} from 'firebase/firestore'
...
const auth=getAuth(app)
const db= getFirestore(app)
export {auth,db}
```
### Load Movies from DB
4. Crate a ***MovieList.jsx*** component inside `src>component` and add this component
inside ***Home.jsx***

5. In ***MovieList.jsx*** import `collection` and `getDocs` from `firebase/firestore`
and import `db` from `init-firebase.js` 

6. Now get docs from database using `db` and collection name and map data into local variable
and set it into state variable.

***MovieList.jsx***
```bash
import React, { useState,useEffect } from 'react'
import { collection,getDocs } from 'firebase/firestore'
import { db } from '../lib/init-firebase'

const MovieLists = () => {
    const [movies, setMovies] = useState([])

    const getMovies=()=>{
        const movieCollectionRef=collection(db,'movies')
        getDocs(movieCollectionRef)
            .then(res=>{
                console.log(res.docs)
                const movs= res.docs.map(doc=>({
                    data:doc.data(),
                    id:doc.id
                }))
                setMovies(movs)
            })
            .catch(error=>console.log(error.message))
    }

    useEffect(() => {
      getMovies()
    
    }, [])
    

  return (
    <div>
        <h3>
            Movie Lists
        </h3>
        <button onClick={()=>getMovies()}>Refresh Movies</button>
        <ul>
            {
                movies.map(movie=>(
                    <li key={movie.id}>{movie.data.name}</li>
                ))
            }
        </ul>

    </div>
  )
}

export default MovieLists
```
### Add Movie
7. Create a ***AddMovie.jsx*** component and add a simple `form` , `input` and `submit` 
button. Add this component into ***Home*** page.

8. Import `collection`, `addDoc` from `firebase/firestore` and `db` from `init-firebase`. 
Now simply pass `name` property through `addDoc` method.

***AddMovie.jsx***
```bash
import React, { useState } from 'react'
import { collection,addDoc } from 'firebase/firestore'
import { db } from '../lib/init-firebase'

const AddMovie = () => {
    const [name, setName] = useState('')

    const handleSubmit=(e)=>{
        e.preventDefault()
        if(name=='') {return}

        const moviesCollectionRef=collection(db,'movies')
        addDoc(moviesCollectionRef,{name: name})
            .then(res=>{
                console.log(res.id)
            })
            .error(err=>{
                console.log(err.message)
            })

    }
  return (
    <div>
        <h4>Add Movie</h4>
        <form onSubmit={handleSubmit}>
            <input type="text" value={name} onChange={(e)=>setName(e.target.value)} />
            <button type='submit'>Add Movie</button>
        </form>
    </div>
  )
}

export default AddMovie
```

9. As we are using `movieCollectionRef` in every component for `movies` collection reference,
lets seperate this into ***firesotre.collection.js*** and export it. We will use this movieCollectionRef
in all component where we need `movies` collection.

***firesotre.collection.js***
```bash
import { collection } from "firebase/firestore";
import { db } from "./init-firebase";

export const movieCollectionRef=collection(db,'movies')
```
import `movieCollectionRef` into ***AddMovie***  and use it inside `addDoc`
function.
### Edit Movie
10. Edit movie will be almost similar to Add move but here we will get the
movie id and use it to update the movie. Instead of collectionRef here we need
documentRef of the particular document that we need to update.
```bash
const docRef=doc(db,'movies',id)
```
11. Then we'll use `updateDoc` function and with `docRef` and `fieldName:fieldValue`. `updateDoc` will
only update the specified field.
```bash
updateDoc(docRef,{name: name})
    .then(res=>{
        console.log(res)
    })
    .error(err=>{
        console.log(err.message)
    })
```
12. However if we use `setDoc` instead of `updateDoc` then whole document will
be replace by new field value.
```bash
setDoc(docRef,{age: 12})
    .then(res=>{
        console.log(res)
    })
    .error(err=>{
        console.log(err.message)
    })

```
13. Include `EditMovie.jsx` edit movie into `Home.jsx`
***EditMovie.jsx***
```bash
import { updateDoc,setDoc } from 'firebase/firestore'
import React, { useState } from 'react'
 import { doc } from 'firebase/firestore'
import { db } from '../lib/init-firebase'

const EditMovie = () => {
    const [name, setName] = useState('')
    const [id, setId] = useState('')

    const handleSubmit=(e)=>{
        e.preventDefault()
        if(name==='' || id==='') {return}

        const docRef=doc(db,'movies',id)

        updateDoc(docRef,{name: name})
            .then(res=>{
                console.log(res)
            })
            .error(err=>{
                console.log(err.message)
            })
        // setDoc(docRef,{age: 12})
        //     .then(res=>{
        //         console.log(res)
        //     })
        //     .error(err=>{
        //         console.log(err.message)
        //     })

    }
  return (
    <div>
        <h4>Edit Movie</h4>
        <form onSubmit={handleSubmit}>
            <label>Movie Id</label>
            <input type="text" value={id} onChange={(e)=>setId(e.target.value)} />
            <label>Movie Name</label>
            <input type="text" value={name} onChange={(e)=>setName(e.target.value)} />
            <button type='submit'>Edit Movie</button>
        </form>
    </div>
  )
}

export default EditMovie
```

### Delete Movie
13. Add a button to inside movie list item in ***MovieList.jsx*** and use `deleteDoc` to delete the item.

***MovieLists.jsx***
```bash
import React, { useState,useEffect } from 'react'
import { collection,deleteDoc,doc,getDocs } from 'firebase/firestore'
import { db } from '../lib/init-firebase'
import { movieCollectionRef } from '../lib/firestore.collections'

const MovieLists = () => {
    const [movies, setMovies] = useState([])

    const getMovies=()=>{
        // const movieCollectionRef=collection(db,'movies')
        getDocs(movieCollectionRef)
            .then(res=>{
                console.log(res.docs)
                const movs= res.docs.map(doc=>({
                    data:doc.data(),
                    id:doc.id
                }))
                setMovies(movs)
            })
            .catch(error=>console.log(error.message))
    }

    useEffect(() => {
      getMovies()
    
    }, [])
    
    const deleteMovie=(id)=>{
        const docRef=doc(db,'movies',id)
        deleteDoc(docRef)
            .then(()=>console.log('Document deleted.'))
            .catch(err=>console.log(err.message))

        alert(id)
    }

  return (
    <div>
        <h3>
            Movie Lists
        </h3>
        <button onClick={()=>getMovies()}>Refresh Movies</button>
        <ul>
            {
                movies.map(movie=>(
                        <li key={movie.id}>
                            {movie.data.name } : {movie.id}
                            <button onClick={()=>deleteMovie(movie.id)}>Delete</button>    
                        </li>
                ))
            }
        </ul>

    </div>
  )
}

export default MovieLists
```

### Realtime Data update
14. For realtime data update create ***RealtimeMovies.jsx*** inside `src>components` and user `onSnapshot`
to get realtime data update.
***RealtimeMovies.jsx***
```bash
import { onSnapshot } from 'firebase/firestore'
import React, { useState,useEffect } from 'react'
import { movieCollectionRef } from '../lib/firestore.collections'

const RealtimeMovies = () => {
    const [movies,setMovies]=useState([])

    useEffect(() => {
      const unSubscribe = onSnapshot(movieCollectionRef,snapshot=>{
        setMovies(snapshot.docs.map(doc=>({id:doc.id,data:doc.data()})))
      })
    
      return () => {
        unSubscribe()
      }
    }, [])
    

  return (
    <div>
        <h3>Realtime Movies</h3>
        <ul>
            {
                movies.map(movie=>(
                        <li key={movie.id}>
                            {movie.data.name } : {movie.id}
                        </li>
                ))
            }
        </ul>
    </div>
  )
}

export default RealtimeMovies
```
## Acknowledgements

 - [Authentication](https://youtu.be/LCAXfjb0oYo)
 - [CRUD](https://youtu.be/F7t-n5c7JsE)
 

