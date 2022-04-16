## 1. Build Servers

#### 1.1 Create Build Server Image
Enter the folder where Dockerfile is, run command:

```
docker build -t buildserver
```

The command will create a docker image called buildserver. We will use the docker image to create a docker in which we compile Ejabberd's source code.


#### 1.2 Checkout Ejabberd's source codes.

Check out Ejabberd's source code from https://github.com/liangdefeng/ejabberd.git to a folder, and switch to 20.12_for_spokechat branch. 

```
git clone https://github.com/liangdefeng/ejabberd.git
cd ejabberd
git checkout 20.12_for_spokechat

cd <where the Ejabberd's repository is checked out>
```

#### 1.3 Run Build Server.

```
docker run --rm -v $(pwd):$(pwd) --name buildserver -it buildserver

```

#### 1.4 Compile Ejabberd's source codes.

###### 1.4.1 After enter the build server, active Erlang

```
. /opt/erlang/21.3.5/activate

```

###### 1.4.2 compile source codes.

```
./autogen.sh
./configure
make

```

###### 1.4.3 Exit the build server.

```
exit
```

The beam files can be found in ebin folder. We will copy the beam file from the folder to the Ejabberd server.


## 2. Ejabberd Servers

#### Enter the folder where docker-compose.yml is, run command:

```
docker-compose up
```

The command will start up three docker servers:
 - main: The Ejabberd server.
 - mysql: The MySQL server Ejabberd connects to.
 - adminer: The admin page to view the mysql database.


#### Copy the modified modules to the Ejabberd server.

Example: copy the mod_offline module to the servers.

```
docker cp <where the Ejabberd's repository is checked out>/ebin/mod_offline.beam main:lib/ejabberd-20.12.0/ebin/

docker exec -it main bin/ejabberdctl debug
l(mod_offline).

```

#### URLs to access various server.

 - http://localhost:5280/admin - Ejabberd's admin page.
 	
 	
 - http://localhost:8080/ - The page to view mysql database.
 
 	System: MySQL
 	
 	Server: mysql
 	
 	Username: ejabberd
 	
 	Password: ejabberd
 	
 	Database: ejabberd

## 3. How to verify.

#### 3.1 Account creation.
Create two accounts: user1 and user2, let them become each other's friend.

#### 3.2 Send offline messages
user2 is offline, user1 sends user2 two messages:
 - One message without persist element.
 - Another message with persist element.
 
Example messages:

```

<message to='user2@localhost' id='Ko1mN-131' type='chat'>
	<body>offline message without persist</body>
</message>


<message to='user2@localhost' id='Ko1mN-132' type='chat'>
	<body>offline message with persist</body>
	<persist/>
</message>

```

#### 3.3 Check spool table in database.

 
The value of the persist field of the Ko1mN-131 message is 0; and its value of the Ko1mN-132 message is 1.


#### 3.4 Remove old messages from Ejabberd.

In the Ejabberd server, run the command:

```
$EJABBERD_HOME/bin/ejabberdctl delete_old_messages 0 
```

#### 3.5 Check the spool table again.

You will see that the Ko1mN-132 message is still in the spool table.

#### 3.6 user2 logs in and check spool table again.

user2 will receive the Ko1mN-132 message, and if you will see that the Ko1mN-132 message disappear in spool table.  

