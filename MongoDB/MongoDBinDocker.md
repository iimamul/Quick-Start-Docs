
# Install MongoDB in Docker Container

Here is the two way to install MongoDB in Docker Container. First one without authentication and second one with authentication.



## First Method (Simple way)

Pull docker mongo images first then  fllow the process

```bash
docker pull mongo
docker create mongo
docker ps –a
docker run --name mongo_v1 -d -p 27023:27017 mongo
mongosh mongodb://localhost:27023
```

## Second Method (With Authentication)

Run docker mongo image in 27000 port and create root user and password

```bash
docker run -d --name sampleDb -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=867 -p 27000:27017 mongo
docker container exec -it sampleDb bash
mongosh mongodb://admin:867@localhost:27000
```

After connecting with root user to mongoDb. Create user for only Customers database

```bash
use customers
db.createUser({	
    user: "web-app",
    pwd: "eureka",
    roles: [{role: "readWrite", db: "customers"}]
})
```

## Acknowledgements

 - [Add security to mongoDb docker container](https://dev.to/blessedtawanda/how-to-add-security-to-your-mongodb-docker-container-4acc)
 - [Running mongoDb in a docker container](https://www.youtube.com/watch?v=NEPZqSvKx40)


