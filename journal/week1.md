# Week 1 — App Containerization

## Required Homework
### 1.1 Containerize Application (Dockerfiles)
I start with the Dockerfile below:

![Dockerfile](assets/Wk1Dockerfile.png)

Then, run Flask:
```
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..
```

Port 4567 should be running through Gitpod and once I unlock the port, I click on the URL and add ``` /api/activities/home ``` at the end.
The JSON code apprears as below:

![JSON](assets/Wk1Json.png)

To build the Docker container, I run the following command:
```
docker build -t  backend-flask ./backend-flask
```

You can see the steps of the container being built like layers. The last lines confirm the container was successfully built.

![Buildbackenddocker](assets/Wk1Buildbackenddocker.png)

The ``` backend-flask ``` and ``` python ``` images were built and are seen by running the command ``` python images ``` as below:

![Dockerimages](assets/Wk1Dockerimages.png)

Because the backend and frontend environment variables are already set in our local machine, I can run ``` docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask ``` where ``` -e ``` will pipe them from my machine.

Port 4567 should be running through Gitpod and once I unlock the port, I click on the URL and add ``` /api/activities/home ``` at the end to see JSON

Next, run the following commands:
``` 
cd frontend-react-js
npm i 
cd ..
```

Then, I create a ``` Dockerfile ``` under ``` frontend-react-js ``` containing the following:
``` 
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```
### 1.2 Containerize Application (Docker Compose)
First, I create a ``` docker-compose.yml ``` at the root of my project. With this, I am able to run multiple containers at the same time.

![DockerComposeFile](assets/Wk1DockerComposeFile.png)

Then, I right-click on the newly created yml file and click the command compose up. It's basically doing a docker build and docker run on both of the containers.

![Dockercomposeup](assets/Wk1Dockercomposeup.png)

I now see port 4567 and port 3000 running and make sure to unlock both of them.

![OpenPorts](assets/Wk1OpenPorts.png)

I click the URL for port 3000 and see the Cruddur home page below. This means that the front end and back end are communicating and multiple containers are running.

![CruddurFrontend](assets/Wk1CruddurFrontend.png)

### 2. Document the Notification Endpoint for the OpenAI Document
Here is where I am documenting:
![OpenAPI](assets/Wk1OpenAPI.png)

If I go back to the Cruddur page, I can log in by using mock authentication. This is not connected to a database. Instead, it's running off of cookies.
Once I am logged in, I can see that all pages are completed, except the "Notifications" page. I begin by adding the notification endpoint ``` /api/activities/notifications ``` to the ``` openapi-3.0.yml ``` file as shown below:

![openapiNotificationEndpoint](assets/Wk1openapiNotificationEndpoint.png)
    
### 3. Write a Flask Backend Endpoint for Notifications
I go to ``` backend-flask/app.py ``` to add the ``` @app.route("/api/activities/notifications", methods=['GET']) ``` Flask route. <br> 
I also add ```from services.notifications_activities import * ``` to the ```app.py``` file. <br>
Then, I create ``` notifications_activities.py ``` under ``` backend-flask/services ``` with the following code as shown below:

![FlaskEndpoint&NotificationsActivitiesServices](assets/Wk1FlaskEndpoint&NotificationsActivitiesServices.png)

Port 4567 should be running through Gitpod and once I unlock the port, I click on the URL and add ``` /api/activities/notifications ``` at the end to see JSON

### 4. Write a React Page for Notifications
I go to ``` frontend-react-js/src/App.js ``` and add ```import NotificationsFeedPage from './pages/NotificationsFeedPage'; ``` along with adding
```
{
path: "/notifications",
element: <NotificationsFeedPage />
},
```

Then, I create the files ``` NotificationsFeedPage.js ``` and ``` NotificationsFeedPage.css ``` under ``` frontend-react-js/src/pages ```

Next, we copy the contents of  ``` HomeFeedPage.js ``` and paste it into ``` NotificationsFeedPage.js ``` and change line 1 to ``` import './NotificationsFeedPage.css'; ```

I also changed line 23 to ``` const backend_url = '${process.env.REACT_APP_BACKEND_URL}/api/activities/notifications' ```

When I click on the Notifications tab on Cruddur, I can see the following which means we are done with the frontend!

![CruddurNotifications](assets/Wk1CruddurNotifications.png)

### 5. Run DynamoDB Local Container and ensure it works
I open the ```docker-compose.yaml ``` file and copy the ```dynamodb-local``` and the ```portgres``` code into it as shown below:

![DynamoDBDatabases](assets/Wk1DynamoDBDatabases.png)

Then, I right-click on this file and click compose up. I confirm that port 8000 is open and continue to create a DynamoDB database as shown below:

![DynamoDBDatabases](assets/Wk1DynamoDBDatabases.png)

I can list the tables as shown below:

![DynamoDBTable2](assets/Wk1DynamoDBTable2.png)

### 6. Run Postgres Container and ensure it works
First, I open the ```gitpod.yml``` file and insert the following client library for Postgres to be able to interact with the server:
```
  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```

Then, we run these lines:
```
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
```

To confirm the connection was made, I run the following command and I should see the following table:

![Postgresconnection2](assets/Wk1Postgresconnection2.png)

Next, I install a PostgreSQL client as an extension and add it to the ```gitpod.yml``` file by clicking on the gear icon and choosing that option. <br>
The line ```cweijan.vscode-postgresql-client2``` should apprear at the end of the file.

I then click on the database explorer icon and click the add icon. After filling out the follwoing fields, I get a successful connection message.

![Postgresconnection](assets/Wk1Postgresconnection.png)
