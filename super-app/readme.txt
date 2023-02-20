How to Configure MySQL
Add the below content in your docker-compose.yml file:

version: '3.4'
services:
  super-app-db:
    image: mysql:8.0.28
    environment:
      MYSQL_DATABASE: 'super-app'
      MYSQL_ROOT_PASSWORD: '$SuperApp1'
    ports:
      - '3306:3306'
    expose:
      - '3306'
Under the services section we will list all the types of applications to be configured.

To start with we configure a super-app-db service which pulls a Docker image of MySQL with version 8.0.28.

Next, we instruct the container to create a database name super-app with root as default user and it’s password set to $SuperApp1.

Lastly, since the default port for MySQL is 3306, we are mapping it to the container’s port 3306 and exposing that port for access.

Once the above file is created, run the below command to get your Docker image created and run it as a container.

docker compose up
The MySQL image will be pulled and then Docker will spin up a container to run this image. MySQL server can be verified by connecting it via a MySQL client.

If not, don't worry – we will see below how to connect to it via one of our applications. As long as the container is not deleted the tables will be persisted.

Let’s configure our next application NodeJS.

How to Configure NodeJS
We will create a very simple Express Node application. In order to do so, create a folder named node inside our super-app folder.

Add the files server.js, package.json and Dockerfile in the node folder.

server.js:

const server = require("express")();
server.listen(3000, async () => { });
server.get("/super-app", async (_, response) => {
    response.json({ "super": "app" });
});
package.json:

{
    "name": "super-app-node",
    "dependencies": {
        "express": "^4.17.1"
    }
}
Dockerfile:

# Download the slim version of node
FROM node:17-slim

# Set the work directory to app folder. 
# We will be copying our code here
WORKDIR /node

#Copy package.json file in the node folder inside container
COPY package.json .

# Install the dependencies in the container
RUN npm install

# Copy the rest of the code in the container
COPY . .

# Run the node server with server.js file
CMD ["node", "server.js"]

# Expose the service over PORT 3000
EXPOSE 3000
Here we are creating a Node application that returns JSON when we hit localhost:3000/super-app in the browser. Now, we are not directly going to run the project from this folder.

Instead, go back to your super-app folder and append the below lines to your docker-compose.yml file:

  super-app-node:
    build: ./node
    ports:
      - "3000:3000"
We are simply mentioning to create a service named super-app-node. We are also mapping the container port to the host port 3000.

Finally run the below command to run your two containers (MySQL and NodeJS):

docker compose up
Now if you hit localhost:3000/super-app you will see a response {“super”:”app”}. Simultaneously your MySQL service is also. Yay! We have successfully created two containers using a docker compose file.
