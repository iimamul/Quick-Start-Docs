## Docker

- **Images** are like blueprint for the container which holds, *runtime environment*, *application code*, *Other dependency and configurations* and *commands*.
- **Container** is created when we run a image. It's a *isolated process* that can run our application exactly how instructions are written in image.

* * *

### Docker Image

- **Images** are made up from several *layers*. Parent layer includes the OS and the runtime environment like *node*. From docker hub we can download these parent images.

- Lets get our first **node** image from docker hub. We can get/pull this image by this command `docker pull node` which can be found on the docker hub.
- In **supported tags** section in docker hub there are many tags
    - **17-alpine** means node version 17 and *alpine* (light weight linux distribution)
- after running this `docker pull node` we can see this image on docker desktop *images* menu. Click **run** button on right side of the image to run this image as container.

* * *

### Create our own docker image

* * *

***Dockerfile***

- **Dockerfile** holds all the information and command to create a image
    
- Create a *react or express* app without *node_module*.
    
- Create a **Dockerfile** in root directory name as `Dockerfile` without any extension and *D* capital form.
    
- write the following instruction in the *Dockerfile*
    
    - Here `FROM node:19-alpine` will pull node 19 alpine to build the image
    - `WORKDIR /docApp` when we `RUN` commands in docker after this command docker will run command to that directory.
    - `COPY . .` will copy all files from root directory(where docker file is located) to the `/docApp` directory. If we didn't mentioned `WORKDIR /docApp` before then it'll copy it to the `root` directory instead of `/docApp`.
    - `RUN npm install` will run the command inside docker image.
    - `EXPOSE 3000` this will expose the container process in port 3000 not by the host computer.
    - `CMD ["npm"," start"]` this command will be run in runtime when the container begin to run.
    - Now run this command on vs code console
        `docker build -t myapp .`
        - Here `-t` (tag) is used to give a name to the image. after this the image name is given and `.` indicates the *Dockerfile* directory.
    - we can see our image in docker desktop app.

* * *

### docker ignore

- create a `.dockerignore` file in the root directory.
- mention all the file name and folder name that we don't to copy while creating image like local *node_module*

* * *

### Run container

- run container from docker desktop app by clicking run button on image.
- after clicking run button we can see container name and **port** configure option. port configure option won't be there if we dont add `EXPOSE` on **Dockerfile**.

* * *

### Run / Stop container from command line

- `docker images`: show all the images

- `docker run myapp`: To run the image, here **myapp** is the name of the image.
- `docker run --name myappContainer myapp`: this will create a container from *myapp* image. Here **myappContainer** is the container name set by `--name` flag.
- `docker ps`: shows list of running containers, here *ps* stands for *processes*.
- `docker stop myappContainer`: running container can be stopped by it's container name or container id.
- `docker run --name myappContainer2 -p 5000:3000 -d myappImage`
    - here `-p` stands for publish. it publish or map container port `3000` to the host computer `5000` port.
    - `-d` is detached flag, if we use it our terminal will be in detached mode that means process won't block our terminal.
    - after that we mention the image name.
- `docker ps -a` show all the container.
- `docker start containerName` starts the stopped container. Insted of container name we can give container id or id initials.

* * *

### Layer Caching

**Dockerfile**

```Dockerfile
FROM node:19-alpine

WORKDIR /docApp

COPY . .

RUN npm install

EXPOSE 3000

CMD ["npm","start"]
```

- Here in Dockerfile each line creates a new *layer* on to the docker image. Each time we change a layer that changes reflects to the image.
- If we change something in our code we need to create a new image but every time we do not need to get node module by *npm install*.
- If we create a image again by changing our code using this command `docker build -t myapp2 .`. This will create a new image file but docker will used previously cached layer if necessary.
    - here we see `COPY . .` step runs again because we changed something in *app.jsx* code and `npm install` layer runs again because it's previous layer changed. because of a layer is based or top of previous layer if a layer changed, subsequent all layer will run again.
- we can use previously installed node_module as cached by changing in our **Dockerfile** code layer.
- **Dockerfile**

```Dockerfile
    FROM node:19-alpine

    WORKDIR /docApp

    COPY package.json .

    RUN npm install 

    COPY . .

    EXPOSE 3000

    CMD ["npm","start"]
```

- Here if build image from this docker file it'll use cached `RUN npm install` command from previous build. we copied `package.json` before `npm install` because without it necessary node package won't be installed. after that we copied all of our source code.
    - here we can see all layers before `COPY . .` is cached from previous build and this build time was super fast because of using previous cached layer.

* * *

### Delete images

- `docker image rm myapp`
    this will remove *myapp* image.
- `docker image rm myapp5 -f`
    this will remove a image forcefully. if a image is used by a container by default it's not allowed to remove that image. In that case we can use `-f` flag.
- `docker image rm myapp myapp2`
    we can remove multiple image by single command like this.

* * *

### Parent image conversion

- node `19-alpine` tag name means node 19 on alpine linux distribution.
- `docker system prune -a` command will delete all *images*, *containers*, and *volumes*.

* * *

### Create image with tag

- `docker build -t myapp:v1` will create an image with tag name *v1*.
- `docker run --name myappContainerV1 -p 2000:4000 myapp:v1` with this comman we can build a container with `myapp:v1` image. Like this we can create container with different version of image like `myapp:v2` , `myapp:v3` .

* * *

### Volumes

> Once a image is built it becomes read-only, we cant change it anymore. If change in a code we have to build a new image and a new container to see the changes. Volumes can resolve this problem.

- Volumes will allow us to map the project folder inside the container so if we change anything then without building an image we can get the changes inside the container.
- *Image* itself doesn't change, volumes just give us way to map directory between container and host computer. if we want to update the `image` or `container` we have to run *docker build* again.
- To implement this we have to add `nodemon` in docker file. nodemon package will check the file changes and restard the server if any file changes.

    `RUN npm install -g nodemon`

    `docker run --name myappContainer2 -p 4000:3000 -rm -v D:\Personal\Projects\ReactWithVite\react-without-vite:/docApp myapp`