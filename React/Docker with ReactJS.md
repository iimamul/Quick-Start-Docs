## Create react app

1.  At the begining we need a react app. So create a react app with the help of **vite**.
    `npm create vite@latest`
    
2.  Create **Dockerfile** inside the react project and write the following code inside that.
    
    ```Dockerfile
    FROM node
    WORKDIR /app
    COPY package.json .
    RUN npm install
    COPY . .
    EXPOSE 3000
    CMD ["npm","run","dev","--host"]
    ```
    
3.  Now run this command on terminal to build a image
    `docker build -t react-image .`
    
4.  Run the image in a container
    `docker run -d -p 9000:3000 --name react-app react-image`
    
5.  To run **vite-react** app in docker container custom port we've to add custom port in **vite.config.js** file.
    
    ```js
    import { defineConfig } from 'vite'
    import react from '@vitejs/plugin-react'
    
    // https://vitejs.dev/config/
    export default defineConfig({
      plugins: [react()],
      server: {
        host: '0.0.0.0',
        port: 3000
      }
    })
    ```
    
    and add this line to **Dockerfile**
    `CMD ["npm","run","dev","--host"]`
    
6.  we can connect file system of our container with this command and check our files with this command.
    `docker exec -it react-app bash`
    
7.  Now add a `.dockerignore` file to ignore some file or folder copied over docker image. now add those file or folder name those we don't want to copied over our docker image.
    
    ```dockerignore
    node_modules
    Dockerfile
    .git
    .gitignore
    .dockerignore
    .env
    ```
    
8.  Now rebuild the image, and create the container again.
    
    ```bash
    docker build -t react-image .
    docker run -d -p 9000:3000 --name react-app react-image
    ```
    
* * *
## Bind mount to sync source code
* * *

9.  Now if we make any changes in our local file it won't change our code inside the container because it was build before changes has been made. We can use volume to fix this issue for our dev environment.
    
10. `docker run -v C:\Users\nayeem\Desktop\react\react-vite-docker\src:/app/src -d -p 9000:3000 --name react-app react-image`
    if we add `-v localdirectory:containerDirectory`, it'll sync to the container directory from our local directory if any changes been made.
    
11. We can make the directory short by this short hand syntax. if we are using powershell we can use `pwd` this will grab our current directory.
    `docker run -v ${pwd}\src:/app/src -d -p 9000:3000 --name react-app react-image`
    If we are on the command line
    `docker run -v %cd%\src:/app/src -d -p 9000:3000 --name react-app react-image`
    If we are on linux
    `docker run -v $(pwd)/src:/app/src -d -p 9000:3000 --name react-app react-image`
    
12. If it doesn't work then in windows host machine we need to pass a enviroment variable like this.
    `docker run -e CHOKIDAR_USEPOLLING=true -v ${pwd}\src:/app/src -d -p 9000:3000 --name react-app react-image`
    now if we make any change in local code it'll show in running container web page.
    
13. If we check our files inside container now we won't find those file we mentioned in our **.dockerignore** file.
    `docker exec -it react-app bash`
    
14. Bind mount create a minor issue, it'll sync both way. Local change reflect to container, container changes reflect to local codes. But we don't want any changes in container files sync to local files. We can do this by **bind mount readonly**.
    `docker run -e CHOKIDAR_USEPOLLING=true -v ${pwd}\src:/app/src:ro -d -p 9000:3000 --name react-app react-image`
    Now if we access into the container `docker exec -it react-app bash` and navigate to the **/src** `cd src` and try to create a file by `touch filename`, it will show *cannot touch 'filename': Readonly file system*.
    

* * *

## Docker Compose

* * *

15. Instead of executing multiple command like build, run in terminal we can create docker compose to execute them.
16. Create **docker-compose.yml** in root directory and put the following code into it.

```yml
version: "3"
services:
  react-app:
    build: .
    ports:
      - "9000:3000"
    volumes:
      - ./src:/app/src
    environment:
      - CHOKIDAR_USEPOLLING=true
```

In *yml* file spacing is important. in first line version is `docker-compose` version number. In `services` container commands will be here. **Tab Space** will point to parent child relation. `-` will be written if mutiple item could be mentioned there.
16. After writting **docker-compose.yml**, write this command on terminal to deploy the containers
    `docker-compose up -d` 
here `-d` is for detached mode.
17. `docker-compose --help` will show all the options. `docker -compose up --help` will show all the different flags that we got here.
18. Now we can bring down all the container that we brought up by `docker-compose down`
19. docker by default won't build a image if a image of that name already exist then we have to prvide this command in terminal.
`docker-compose up -d --build`
* * *
## Dev and Production enviroment workflow
* * *
> Till now we've seen how to run react in developer mode now we'll se how we can run  developer and production mode side by side. when we run project on dev server it's run React dev server which is not suitable for production. For production we need production grade server like nginx or apache which can process thousands of requests.
20. We need multistage build for this, 1st stage for dev environment 2nd stage for produciton environment.
![3f18dad6142365e206ab11db51eeb13b.png](:/d68fb7b90e094bef890983132aa6f879)
21. Two different Dockerfile need for this, dev dockerfile and production docker file.
22. Rename our existing **Dockerfile** to **Dockerfile.dev** and changing this won't keep thing as before. we need to make some other changes because by default docker only know **Dockerfile**.
23. When building docker image we need to pass one mor flag that is `-f` for  filename then provide the full path of the Dockerfile then `.` for all file of the current directory.
`docker build -f ./Dockerfile.dev .`

24. So, in **docker-compose.yml** we need to change these follwings. Add context and dockerfile in build section.
	```yml
	version: "3"
	services:
	  react-app:
		build:
		  context: .
		  dockerfile: Dockerfile.dev
		ports:
		  - "9000:3000"
		volumes:
		  - ./src:/app/src
		environment:
		  - CHOKIDAR_USEPOLLING=true

	```
	
25. Create another docker file production named **Dockerfile.prod**.
	```Dockerfile
	FROM node as build
	WORKDIR /app
	COPY package.json .
	RUN npm install
	COPY . .
	RUN ["npm","run","build"]

	FROM nginx
	COPY --from=build /app/dist /usr/share/nginx/html
	```
	here in last command it'll copy from previous stage build, whatever in /app/dist directory to nginx cotainer folder(check docker hub nginx documentation about it's directory). here we copied from *dist* directory because if we run `npm run build` on our local code we'll see a dist folder is created in root directory with build code.
	
26. Now build image with this command. 
`docker build -f Dockerfile.prod -t docker-prod-image .`
and run container with this one
`docker run -d -p 8080:80 --name docker-prod-app docker-prod-image`
27. To add these commands in **docker-compose.yml**, we'll separate docker-compose file into 3 separe files **docker-compose-dev.yml**, **docker-compose-prod.yml**, and **docker-compose.yml**. **docker-compose.yml** will hold the common command that will not vary in production or dev environment.
**docker-compose.yml**
```yml
version: "3"
services:
  react-app:
    build:
      context: .
      dockerfile: Dockerfile.dev
```
**docker-compose-dev.yml**
```yml
version: "3"
services:
  react-app:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "9000:3000"
    volumes:
      - ./src:/app/src
    environment:
      - CHOKIDAR_USEPOLLING=true
```
**docker-compose-prod.yml**
```yml
version: "3"
services:
  react-app:
    build:
      context: .
      dockerfile: Dockerfile.prod
    ports:
      - "8080:80"
```


28. To run our dev container 
`docker-compose -f docker-compose.yml -f docker-compose-dev.yml up -d --build`
To run production container 
`docker-compose -f docker-compose.yml -f docker-compose-prod.yml up -d --build`
Pass `--build` if you want to build the image again. Here file sequence is important **docker-compose.yml** build section will be overriden by **docker-compose-prod.yml**.


## Reference
- [Docker with reactJs ](https://youtu.be/3xDAU5cvi5E?list=LL)
