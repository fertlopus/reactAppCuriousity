### Not a project! Just curious about the nodejs, react and mongo.

---

There are 3 components:
* frontend `>>>` React Single Page Application (SPA)
* backend  `>>>` Nodejs REST API service
* MongoDB  `>>>` Database for data.

All components are **dockerized** and set up a networking between them allowing to communicate with each other.

Pseudo-Schema:

![img](src/imgs/schema.png)


### Networking: 

---

To create better communication between containers you should create a network:

``$ docker network create goals-app-network``

#### To run the project: 

----


1. Install mongoDB using docker container:

    ```$ docker run --name mongodb -v data:/data/db --rm -d --network goals-app-network mongo```
   
After this command you should see up and running **MongoDB container**: 

![img](./src/imgs/mongo_container.png)


If you want an extra layer of security run the container using username and password for the mongoDB:

   ``$ docker run --name mongodb -v data:/data/db --rm -d --network goals-app-network -e MONGO_INITDB_ROOT_USERNAME=<your_username> -e MONGO_INITDB_ROOT_PASSWORD=<your_password>``

And change the connection type string inside `backend/app.js` to:

```javascript
mongoose.connect(
  'mongodb://<your_username>:<your_password>@mongodb:27017/course-goals?authSource=admin',
  {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  },
  (err) => {
    if (err) {
      console.error('FAILED TO CONNECT TO MONGODB');
      console.error(err);
    } else {
      console.log('CONNECTED TO MONGODB');
      app.listen(80);
    }
  }
);
```

2. Inside the `backend` component there is a `Dockerfile`. You need to build the image using the following command:

   ``$ cd backend``
   
   ``$ docker build -t goals-backend .``

   ``$ docker run --name goals-app-backend -v <your_full_path> -v logs:/app/logs -v /app/node_modules --rm -d -p 80:80 --network goals-app-network goals-backend``

The last command uses bind mounts to make data persistence and the full command might look something like this in your environment for this repository:

   ``$ docker run --name goals-app-backend -v /Users/username/project/backend:/app -v logs:/app/logs -v /app/node_modules --rm -d -p 80:80 --network goals-app-network goals-backend``


3. Inside the `frontend` component there is also `Dockerfile`. You need to build the image using the following command:

   ``$ cd frontend``

   ``$ docker build -t goals-frontend .``

   ``$ docker run --name goals-app-frontend --rm -d -p 3000:3000 goals-frontend``

Under the `localhost:3000` you will be able to obtain the UI: 

![](src/imgs/UI.png)





