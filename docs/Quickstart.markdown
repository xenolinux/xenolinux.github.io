
# QUICKSTART FOR INSTALLING  OWNCLOUD SERVER AND CONNECTING TO IT AS A CLIENT

## OWNCLOUD 10.7

Owncloud is open-source file sync, share, and content collaboration software that lets teams work on data easily from anywhere, on any device. It provides access to your data through a web interface while providing a platform to view, sync and share across devices easily. 

### How to install and configure

#### ABSTRACT

Read this page for instructions on installing and configuring an Owncloud server as an administrator.
<br />
<br />

## Chapter 1. Requirements for Installing Owncloud 

Before installing Owncloud, review the following supported system requirements. 

### 1.1 Prerequisites

* Verify if the following options meet the minimum requirement for Owncloud.

### 1.2 System requirements checklist for installing Owncloud 

| Requirements     | Options                                                    |
| -------------    |:-------------------------------------------------------:   |
| Operating System | Ubuntu 16.04 and 18.04      			                        	|
|                  | Debian 7, 8, and 9                                         |
|                  | SUSE Linux Enterprise Server 12 with SP1, SP2, and SP3     |
|                  | Red Hat Enterprise Linux 6.9, 7.3, 7.4, and 7.5 		        |
|                  | Fedora 27, 28, and 29 					                            |
|                  | openSUSE Leap 42.3 and 15 				                        	|    
| Database         |  MySQL or MariaDB 5.5+                                     |
|                  | Oracle 11g                                     		        | 
|                  | PostgreSQL 9                                               |
|                  |SQLite							                                        |
|                  | openSUSE Leap 42.3 and 15 					                        |    
|                  | Web server    						                                  |
|  	           | Apache 2.4 with [prefork and mod_php](https://doc.owncloud.com/server/10.0/admin_manual/installation/manual_installation.html#multi-processing-module-mpm)                          |
| PHP Runtime      | 5.6, 7.0, 7.1, and 7.2      				                        |
| Mobile           | iOS 9.0+                                                   |
|                  | Android 4.0+                                               |
| Web Browser      | Edge (current version on Windows 10)                       |   
|                  | IE11+ (except Compatibility Mode)                          |
|                  | Firefox 57+ or 52 ESR                                      |
|                  | Chrome 66+                                                 |
|                  | Safari 10+   						                                  |
| Desktop          | Windows 7+                                                 | 
|                  | Mac OS X 10.7+ (64-bit only)                               |
|                  | CentOS 6 & 7 (64-bit only)                                 |
|                  | Debian 8.0 & 9.0   					                              |
|                  | Fedora 27 & 28 						                                |
|                  | Ubuntu 16.04 & 18.04					                              |  	
|                  | openSUSE Leap 42.2 & 42.3					                        |
| Hypervisors      | Hyper-V                   					                        |
|                  | VMware ESX                					                        |
|                  | Xen                    					                          |
|                  | KVM							                                          |
| Client Versions  | Desktop Client 2.3.3 					                            |
|                  | iOS App							                                      |
|                  | Android App 					                                    	|

### Memory Requirements

Memory requirements for running an Owncloud server are greatly variable, depending on the numbers of users and files, and volume of the server activity. Owncloud officially requires a minimum of 128MB RAM. But, it is recommended a minimum of 512MB RAM. 

### Database Requirements

The following are currently required if you’re running Owncloud together with a MySQL or MariaDB database:

* Disabled or `BINLOG_FORMAT = MIXED or BINLOG_FORMAT = ROW` configured Binary Logging, see [MySQL / MariaDB with Binary Logging Enabled](https://doc.owncloud.com/server/10.0/admin_manual/configuration/database/linux_database_configuration.html#mysql-mariadb-with-binary-logging-enabled).

* InnoDB storage engine, see [MySQL / MariaDB storage engine](https://doc.owncloud.com/server/10.0/admin_manual/configuration/database/linux_database_configuration.html#mysql-mariadb-storage-engine).

* `READ COMMITED` transaction isolation level, see [MySQL / MariaDB READ COMMITED transaction isolation level](https://doc.owncloud.com/server/10.0/admin_manual/configuration/database/linux_database_configuration.html#mysql-mariadb-read-commited-transaction-isolation-level).
<br />
<br />

## Chapter 2. Installing and Configuring Owncloud Using Docker

This chapter describes how to install and configure the Owncloud server using Docker Compose. 

The process consists of configuring the environment, creating the containers, stopping the containers, connecting users to the Owncloud server using the server's IP address and port 8080 and logging in to the Owncloud server.

### Prerequisites

* The host machine must be able to connect to the Internet.
* Ensure the default port for HTTP, 8080, is accessible. 
* Docker is installed and Docker daemon is running.

#### NOTE
> If you want to run Docker without root privileges, see [Run the Docker daemon as a non-root user (Rootless mode)](https://docs.docker.com/engine/security/rootless/).

### Procedure

<span>1.</span> Verify that Docker is installed.
<br />

```
$ sudo docker info
```
Example:

```
Client:
Context:    default
Debug Mode: false
Plugins:
 app: Docker App (Docker Inc., v0.9.1-beta3)
 buildx: Build with BuildKit (Docker Inc., v0.5.1-docker)
 scan: Docker Scan (Docker Inc., v0.6.0)

Server:
Containers: 4
Running: 4
Paused: 0
Stopped: 0
Images: 21
Server Version: 20.10.5
Storage Driver: overlay2
```
<br />

If you get the output, skip the next step. If you see `docker: command not found`, continue to the next step *Install Docker*.

<span>2.</span> [Install](https://docs.docker.com/engine/install/) Docker.
<br />

<span>3.</span> Create a new project directory.
<br />

```
$ sudo mkdir owncloud-docker-server
```

<span>4.</span> Change the current directory.
<br />

```
$ cd owncloud-docker-server
```

#### NOTE
> Go to the directory where your .yaml and .env files are located to be able to run `docker-compose` commands.

<span>5.</span> Copy docker-compose.yml from the GitHub repository.
<br />

```
$ wget https://raw.githubusercontent.com/owncloud/docs/master/modules/admin_manual/examples/installation/docker/docker-compose.yml
```

<span>6.</span> Create the environment configuration file. The configuration exposes port 8080, to allow HTTP connections. It uses separate MariaDB and Redis containers and mounts the data and MySQL data directories on the host for persistent storage.
<br />

```
$ cat << EOF > .env
OWNCLOUD_VERSION=10.7
OWNCLOUD_DOMAIN=localhost:8080
ADMIN_USERNAME=admin
ADMIN_PASSWORD=admin
HTTP_PORT=8080
EOF
```

#### NOTE
> `ADMIN_USERNAME` and `ADMIN_PASSWORD` do not change between deploys even if the values in the .env file are changed. Do `docker volume prune` to change those values. Which deletesall your data. 

> The following instructions assume you install locally. For remote access, the value of `OWNCLOUD_DOMAIN` must be adapted.

<span>7.</span> Build and start the container
<br />

```
$ sudo docker-compose up -d
```

Example:

```
$ docker-compose up -d          
Docker Compose is now in the Docker CLI, try `docker compose up`

Creating owncloud_mariadb ... done
Creating owncloud_redis   ... done
Creating owncloud_server  ... done
```

<span>8.</span> Check that all the containers have successfully started.
<br />

Example:

```
docker-compose ps   
      Name                    Command                  State               Ports         
-----------------------------------------------------------------------------------------
owncloud_mariadb   docker-entrypoint.sh --max ...   Up (healthy)   3306/tcp              
owncloud_redis     docker-entrypoint.sh --dat ...   Up (healthy)   6379/tcp              
owncloud_server    /usr/bin/entrypoint /usr/b ...   Up (healthy)   0.0.0.0:8080->8080/tcp
``` 

## 2.1 Stop the Containers

<span>1.</span> You can stop the containers using `docker-compose` command. Run the command below.
<br />

```
$ sudo docker-compose stop
```
Example: 

```
$ sudo docker-compose stop     
Stopping owncloud_server  ... done
Stopping owncloud_mariadb ... done
Stopping owncloud_redis   ... done
```

<span>2.</span> Stop and remove containers, along with the related networks, images, and volumes. Run the command below.
<br />

```
$ sudo docker-compose down --rmi all --volumes
```

Example:

```
$ sudo docker-compose down --rmi all --volumes
Removing owncloud_server  ... done
Removing owncloud_mariadb ... done
Removing owncloud_redis   ... done
Removing network owncloud-docker-server_default
Removing volume owncloud-docker-server_files
Removing volume owncloud-docker-server_mysql
Removing volume owncloud-docker-server_redis
Removing image mariadb:10.5
Removing image redis:6
Removing image owncloud/server:10.7
```

<span>3.</span> Verify that containers are removed.
<br />

```
$ sudo docker-compose ps
```

Example:

```
$ sudo docker-compose ps
Name   Command   State   Ports
------------------------------
```
<br />
<br />

## Chapter 3. Installing and Configuring Owncloud Server using Linux Package Manager

This chapter consists of instructions on how to install LAMP, download and install Owncloud source packages, setup MariaDB and configure Owncloud.

### Procedure

<span>1.</span> Install LAMP ( Linux, Apache, MariaDB, and PHP ) server. 
<br />
<span>2.</span> Update the Package Manager for package installation, see [Package manager](https://download.owncloud.org/download/repositories/production/owncloud/).
<br />
<span>3.</span> Download and install OwnCloud, see [Download](https://owncloud.com/download-server/).
<br />
<span>4.</span> [Setup](https://doc.owncloud.com/server/admin_manual/configuration/database/linux_database_configuration.html) MariaDB for OwnCloud.
<br />
<span>5.</span> Log in to the Owncloud server. For more details, see the next chapter *Log in to Owncloud*.
<br />
<br />

## Chapter 4. Log in to Owncloud

You can view the Owncloud user interface by logging into Owncloud.

### Prerequisites

* The Owncloud is installed and configured.
* A running browser to run the URL.

### Procedure

<span>1.</span> Open the URL `http://localhost:8080` in a web browser. 
<br />

![Figure 2.3.1](login.png)


<span>2.</span> Enter the **Username** admin and its **Password**.
<br />

![Figure 2.3.2](login-with-admin.png)


<span>3.</span> Click the arrow to log into the Owncloud User Interface.
<br />

![Figure 2.3.3](login-arrow.png)


## Chapter 5. Adding a User Account as an Administrator

This chapter describes how to add a user account to the Owncloud user interface.

### Prerequisites

* The Owncloud user interface is accessible.
* Being logged into the Owncloud user interface as an administrator.

### Procedure

<span>1.</span> Enter the **Username** and **Password** to log into the Owncloud user interface. 
<br />

![Figure 4.1](login.png)


<span>2.</span> In the top right corner, click **admin**. Click **Users** from the dropdown list.
<br />

![Figure 4.2](click-admin.png)


<span>3.</span> Enter the **username** and **email**. 


![Figure 4.3](enter-username-email.png)
<br />

<span>4.</span> Click the **Create** button.
<br />

![Figure 4.4](create-button.png)
<br />
<br />

## Chapter 6. Installing and Connecting to Owncloud Server Using a Desktop Client.

This chapter describes steps to install and connect to the Owncloud server using a desktop client as a user.

### Prerequisites

* The Desktop Application must be downloaded. For details, see [Download desktop app](https://owncloud.com/desktop-app/).
* You have the URL of the Owncloud server.

### Procedure

<span>1.</span> Open the desktop application in your system. The installation wizard takes you step-by-step through configuration options and account setup.
<br />

<span>2.</span> Click the **Continue** button.
<br />

![Figure 5.2](continue.png)
<br />

<span>3.</span> Click the **Install** button.
<br />

![Figure 5.1](install.png)
<br />

<span>4.</span> Enter the URL for the Owncloud server in the space in front of the **Server Address**.
<br />

<span>5.</span> Click the **Next** button.
<br />

![Figure 5.3](next.png)
<br />

<span>6.</span> Once the installation process is completed, enter the **Username** and **Password**.
<br />

![Figure 5.4](doneinstall.png)
<br />

<span>7.</span> You can sync all of your files on the Owncloud server or select individual folders on the **Local Folder** option. The default local sync folder is ownCloud, in your home directory. You can change the location of the local sync folder.
<br />

![Figure 5.5](connect.png)
<br />

<span>8.</span> Click on the **Connect** button. You will see that Owncloud is synchronizing files with the local folder.

![Figure 5.6](done.png)












