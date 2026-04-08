# DockerImages
Some images created by me for Docker on WSL2

# Installing Mongo DB with Replica Sets

## This guide contains all the steps to create a Mongo DB container that supports replica sets.

### 1) Prelimnary operations

In WSL, create a folder (e.g., "mongodb-container") with the following commands:

1.a) <code>cd ~ </code><br>
1.b) <code>mkdir mongodb-container</code>

Then copy the docker-compose.yml file with the command:

<code>cp -r 'your-compose-file-path/docker-compose.yaml' ~/mongodb-container/</code>

### 2) Creating the Keyfile

To create a Mongo DB with replicas, you need to create a keyfile. To do this, navigate to the mongodb-container folder in WSL
and type the following commands:

<code>openssl rand -base64 756 > mongodb-keyfile</code>

Once this is done, you need to change the file permissions and owner with the following commands:

- Change the owner to UID 999 (the container's internal mongodb user)
<code>sudo chown 999:999 mongodb-keyfile</code>

- Set restrictive permissions
<code>sudo chmod 400 mongodb-keyfile</code>

### 3) Creating containers

If there are any existing Mongo containers, delete them (including the volumes) with the command:

<code>docker compose down -v</code>

Once this is done, run the command from the mongodb-container directory:

<code>docker-compose up -d</code>

### 4) Initializing replicasets:

Type the command:

<code>docker exec -it mongodb-local mongosh -u mongoadmin -p 'm0ng0!2026' --eval "rs.initiate({_id:'rs0', members:[{_id:0, host:'localhost:27017'}]})"</code>

If the response contains { ok: 1 }, then the operation was successful.

Start the containers from docker and check the logs:

<code>docker start mongodb-local<br>
docker start mongo-express-local</code>

Check if they are running:

docker container ls

Go to the logs and check for errors.

<code>docker logs <container-name></code>

### 5) Mapping in the Windows Hosts File

Using any editor, open the Windows hosts file with administrator privileges and insert this line:

<code>127.0.0.1 mongo</code>

Then save the Changes

### 6) How to connect to Mongo DB from an application (e.g., from an API)

Use this connection string:

<code>mongodb://mongoadmin:m0ng0!2026@localhost:27017/cmp-sdn-provider?authSource=admin&replicaSet=rs0&directConnection=false</code>
