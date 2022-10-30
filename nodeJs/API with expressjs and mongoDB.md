
# REST api with Express and MongoDB

A brief description of quick start rest api with express and MongoDB.


## Installation

1. First create a directory and run `npm init` to create **package.json** file.

2. Now install `express` and `nodemon`. nodemon needs to execute and run the server

```bash
npm install express nodemon
```
3. add following script on **package.json**  to run the server.

```json
"scripts":{
    "start":"nodemon server.js"
}
```
4. Create **server.js** file to the root directory with following code.
```js
const express= require('express')

const app = express()

//Routes
app.get('/',(req,res)=>{
    res.send('Here is a simple api route')
})

app.get('/posts',(req,res)=>{
    res.send('Here is the posts endpoint of the api')
})

//server will start listening to the mentioned port
app.listen(5000)
```
5. Run `npm instal mongoose` to install mongoose for `mongodb` database connection.
6. Now use `mongoose` in **server.js** to connect the database.

```js
const mongoose= require('mongoose')

//Connect to DB
mongoose.connect('http://localhost:27000/',()=>
    console.log('Connect to db')
)
```
7. Now install `dotenv` package to get out db connection from `.env` file by this command.
```bash
npm install dotenv
```
8. Create a `.env` file and shift the db connection into it and use
it into **server.js** file by writing following code.

**.env**
```bash
DB_CONNECTION=mongodb+srv://username:password@cluster0.crc0qqp.mongodb.net/?retryWrites=true&w=majority
```
**server.js**
```js
require('dotenv/config')

//Connect to DB
mongoose.connect(process.env.DB_CONNECTION,()=>
    console.log('Connect to db')
)
```
9. We can separate the routes to their own file and import them into
**server.js** file.
**posts.js**
```js
const express= require('express')
const router = express.Router();

// Routes
// reveal on http://localhost:5000/posts
router.get('/',(req,res)=>{
    res.send('Here is the posts route')
})

//  http://localhost:5000/posts/post99
router.get('/post99',(req,res)=>{
    res.send('Here is the posts 99')
})
module.exports=router
```
**server.js**
```js
const express= require('express')
const app = express()
const mongoose= require('mongoose')
require('dotenv/config')

//Import Routes
const postsRoute=require('./routes/posts')

//Middleware [mulitple Middleware can be used for different router]
app.use('/posts',postsRoute)

//Routes
app.get('/',(req,res)=>{
    res.send('We are on base directory')
})


//Connect to DB
mongoose.connect(process.env.DB_CONNECTION,()=>
    console.log('Connect to db')
)

//server will start listening to the mentioned port
app.listen(5000)
```
10. Now we need a `Posts` model with `mongoose`. Create `models>Post.js`
file. In Post.js we'll create Post Schema.
**Post.js**
```js
const mongoose=require('mongoose')

const PostSchema= mongoose.Schema({
    title:{
        type:String,
        required:true
    },
    description:{
        type:String,
        required:true
    },
    date:{
        type:Date,
        default: Date.now
    }

    //also could be written as this wihtout validation
    // title:String,
    // description:String,
    // date:Date
})


module.exports= mongoose.model('Posts',PostSchema)
```
11. Install `body-parser` package to parse the body data into json.
```bash
npm install body-parser
```
12. Add a post route in `routes>posts.js`.Then add this bodyParser 
middleware into **server.js** file so that we can get POST request's
request body in json.

**posts.js**
```js
const express= require('express')
const router = express.Router();
const Post = require('../models/Post')

//Routes
//reveal on http://localhost:5000/posts

router.get('/',(req,res)=>{
    res.send('Here is the posts route')
})

//  http://localhost:5000/posts/post99
router.get('/post99',(req,res)=>{
    res.send('Here is the posts 99')
})


router.post('/',(req,res)=>{
    const post = new Post({
        title: req.body.title,
        description: req.body.description
    })

    post.save()
        .then(data=>{
            res.json(data)
        })
        .catch(err=>{
            res.json({message:err})
        })
})

module.exports= router
```
**server.js**
```bash
const express= require('express')
const app = express()
const mongoose= require('mongoose')
const bodyParser=require('body-parser')
require('dotenv/config')

//Import Routes
const postsRoute=require('./routes/posts')


//Middleware
app.use(bodyParser.json()) //everytime we hit any url this bodyparser will run

app.use('/posts',postsRoute)



//Routes
app.get('/',(req,res)=>{
    res.send('Here is a simple api route')
})


//Connect to DB
mongoose.connect(process.env.DB_CONNECTION,()=>
    console.log('Connect to db')
)

//server will start listening to the mentioned port
app.listen(5000)
```
13. Now add get, delete, and update method to the **posts.js**

```js
const express= require('express');
const { restart } = require('nodemon');
const router = express.Router();
const Post = require('../models/Post')

//Routes
//reveal on http://localhost:5000/posts
//Gets back all the post
router.get('/',async (req,res)=>{
    try{
        const posts= await Post.find()
        res.json(posts)
    }
    catch(err){
        res.json({message:err})
    }
})

//  http://localhost:5000/posts/post99
router.get('/post99',(req,res)=>{
    res.send('Here is the posts 99')
})

//SUBMITS A POST
router.post('/',(req,res)=>{
    const post = new Post({
        title: req.body.title,
        description: req.body.description
    })

    post.save()
        .then(data=>{
            res.json(data)
        })
        .catch(err=>{
            res.json({message:err})
        })
})

//GET SPECIFIC POST
router.get('/:postId',async (req,res)=>{
    try{
        // console.log(req.params.postId)
        const post=await Post.findById(req.params.postId)
        res.json(post)
    }
    catch(err){
        res.json({message:err})
    }
})

//DELETE POST
router.delete('/:postId',async (req,res)=>{
    try{
        // console.log(req.params.postId)
        const removePost=await Post.remove({_id: req.params.postId})
        res.json(removePost)
    }
    catch(err){
        res.json({message:err})
    }
})

//UPDATE A POST
router.patch('/:postId',async (req,res)=>{
    try{
        // console.log(req.params.postId)
        const updatePost=await Post.updateOne({_id: req.params.postId},{$set:{title:req.body.title}})
        res.json(updatePost)
    }
    catch(err){
        res.json({message:err})
    }
})


module.exports= router
```

14. Install `cors` to enable cross domain fetching for api.
```bash
npm install cors
``` 
After installing package just add `cors` middleware in **server.js**
```bash
const cors=require('cors')

app.use(cors())
```
## Acknowledgements

 - [Video Tutorial Reference](https://youtu.be/vjf774RKrLc)

