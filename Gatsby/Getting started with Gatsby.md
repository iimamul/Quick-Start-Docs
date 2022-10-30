
# Getting started with Gatsby

A brief description of Gatsby quick start following this [tutorial](https://youtube.com/playlist?list=PL4cUxeGkcC9hw1g77I35ZivVLe8k2nvjB).


## Process

1. Install gatsby cli

```bash
npm install -g gatsby-cli
```

2. Install gatsby starter hello world project from [here](https://www.gatsbyjs.com/starters/gatsbyjs/gatsby-starter-hello-world/).
`gatsby-starter-hello-world` can be changed with own project name.

```bash
npx gatsby new gatsby-starter-hello-world https://github.com/gatsbyjs/gatsby-starter-hello-world
```
3. Run website on the browser

```bash
gatsby develop
```

4. gatsby routes automatically based on file name and folder directory.
Like `src>pages>pos>salesorder.js` can be accessed with this url `http://localhost:8000/pos/salesorder`.
`index.js` file in root directory will be accessed by this `http://localhost:8000/`. If we
create `index.js` in `pos` folder then it can be accessed by this url `http://localhost:8000/pos`. 

5. To create a custom 404 page. 404 should be added to `src>pages>404.js` folder.
Rename component name of 404 as `PageNotFound` or anything. If we hit a url now
that doesn't exist on production mode then we can see that page.
In developer mode we won't see that page directly.

6. Now create a layout page and use to on every page sothat we can see
navbar, sidebar and footer on everypage.

***Navbar.js***
```js
import { Link } from 'gatsby'
import React from 'react'

const Navbar = () => {
  return (
    <nav>
        <h1>POS</h1>
        <div className="links">
            <Link to="/">Home</Link>
            <Link to="/pos">POS page</Link>
            <Link to="/pos/salesorder">SalesOrder</Link>
        </div>
    </nav>
  )
}

export default Navbar
```

***Layout.js***
```js
import React from 'react'
import Navbar from './Navbar'

const Layout = (props) => {
  return (
    <div className="layout">
        <Navbar/>
        <div className="content">
            {props.children}
        </div>
        <footer>
            <p>Copyright 2022 @ned</p>
        </footer>
    </div>
  )
}

export default Layout
```
***salesorder.js***
```js
import React from 'react'
import Layout from '../../components/Layout'

const salesorder = () => {
  return (
    <Layout>
        <div>SalesOrder</div>
    </Layout>
  )
}

export default salesorder
```

7. Import a css module and add style to the layout. Import css module
using `import * as layoutStyles from '../styles/home.module.css`, if we don't 
add `* as` while importing it won't find the css module.


8. import `useStaticQuery` and get data with graphql query with that. We can use
`useStaticQuery` only one time in a component.

***index.js***
```jsx
import { graphql, useStaticQuery } from "gatsby"
import React from "react"
import Layout from "../components/Layout"

export default function Home() {
  const data =useStaticQuery(graphql`
      {
        site {
          host
          port
        }
      }
  `)
  return (
    <Layout>
      <section>
        <div>
          <h1>Main page</h1>
          <p>Here is the main content address {data.site.host}:{data.site.port}</p>
        </div>
      </section>
    </Layout>
    )
}

```

9.