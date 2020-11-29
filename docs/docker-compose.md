# Docker compose
Docker can create one container with just one image (each image can create only one application). But, if we need to run many applications at the same time? We can use docker compose which groups the images in a document called `docker-compose.yml`.

## Running hello-world image with docker-compose
Lets create a test:
```bash
$ mkdir hello-world             # create some dir
$ cd hello-world                # enter it
$ nano docker-compose.yml       # create and open docker-compose.yml
```
Now, inside the doc you put the basic configuration:
```yml
my-test:
    image: hello-world
```
This tells to docker that the container name is my-test and the image to be runned is `hello-world`.

Now if you see your images:
```bash
$ sudo docker images
```
Probably you will see:
```bash
Output
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

Then run inside the docker-compose.yml directory:
```bash
$ sudo docker-compose up     # You will run the hello-world test script
```

Now, if you run `sudo docker images` again or `sudo docker ps -a` you will be able to see the container created by docker-compose.


## Deploying a project with nginx image in docker-compose
Another test:
```bash
$ mkdir my-second-test
$ cd my-second-test
$ touch docker-compose.yml
$ mkdir app
$ cd app/
$ touch index.html
```

Inside index.html you can put something like:
```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>Docker Compose Demo</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/kognise/water.css@latest/dist/dark.min.css">
</head>
<body>

    <h1>This is a Docker Compose Demo Page.</h1>
    <p>This content is being served by an Nginx container.</p>

</body>
</html>
```

And inside docker-compose.yml you should put this:
```yml
version: '3.7'
services:
  web:
    image: nginx:alpine
    ports:
      - "8000:80"
    volumes:
      - ./app:/usr/share/nginx/html
```

Then, what we have? A html test file and the docker compose yml file. The yml file shows the version of docker-compose configurations and the services (in this case, just `web`). The web service has the image, that is nginx (it will deploy our html file), also has the port of deploy (8000 in our machine, but in the container it will be 80, which is the nginx port) and the volumes, which is where our application will be saved in the containers (we are defining that the app folder inside this directory will be saved as `/usr/share/nginx/html` in the container, which is the nginx projects path).

To run it:
```bash
$ sudo docker-compose up -d     # -d is to run in second plan, so you can use the terminal
```

If you run `sudo docker-compose ps` or `sudo docker ps` you will see something like this:
```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS                  NAMES
ef2d797f0157        nginx:alpine        "/docker-entrypoint.…"   2 minutes ago       Up About a minute           0.0.0.0:8000->80/tcp   files_web_1
```

Then, just open `localhost:8000` and see the project running:

![Docker compose nginx deploy](images/docker-compose-nginx-deploy.png)

If you see Forbidden 403, probably its because the folder in the container doesnt have permission, so just run:
```bash
$ sudo docker exec container_id chmod 755 /usr/share/nginx/html/index.html
```

## Some more docker-compose commands
How docker-compose works with directories containers, you dont pass any name or id in the commands (just stay in the container directory).

To list the containers
```bash
$ sudo docker-compose ps
```

Upping a container
```bash
$ sudo docker-compose up    # can use -d to second plan container
```

Down a container
```bash
$ sudo docker-compose down
```

See logs
```bash
$ sudo docker-compose logs
```

Pause/unpause a container
```bash
$ sudo docker-compose pause
$ sudo docker-compose unpause
```

Stop the container
```bash
$ sudo docker-compose stop
```

You can just run `docker-compose` and see the full list:
```html
Commands:
  build              Build or rebuild services
  config             Validate and view the Compose file
  create             Create services
  down               Stop and remove containers, networks, images, and volumes
  events             Receive real time events from containers
  exec               Execute a command in a running container
  help               Get help on a command
  images             List images
  kill               Kill containers
  logs               View output from containers
  pause              Pause services
  port               Print the public port for a port binding
  ps                 List containers
  pull               Pull service images
  push               Push service images
  restart            Restart services
  rm                 Remove stopped containers
  run                Run a one-off command
  scale              Set number of containers for a service
  start              Start services
  stop               Stop services
  top                Display the running processes
  unpause            Unpause services
  up                 Create and start containers
  version            Show version information and quit
```

## Using docker-compose to create a laravel environment
Now, you know the basic of docker-compose and can create multiple containers by using it. A good example is a Laravel environment (you can find it in the files directory in this repository). The versions of the docker-compose iamges are a bit outdated, but for our example will work (of course you should build your docker-compose file with the latest versions of the images).

To run the Laravel-environment you can do the following:

Enter the docker-compose.yml folder and run:
```bash
$ sudo docker-compose up -d
```

It will take a while, because it is installing all dependences and creating the containers. After this, if you run `sudo docker ps` you can see all the 4 containers created.

Now, we gonna clone a Laravel git project to our container, so we can run it in the container and not in our machine:
```bash
$ sudo docker exec -it php /bin/bash
$ git clone <git-project>
$ exit
```
Now, lets update the docker-compose.yml file with our project, so, every time we enter the container we will enter in the project folder. For it, stop the containers:
```bash
$ sudo docker-compose stop
```
Modify the docker-compose.yml, where you see ".:/application" change to "./[your-project-folder]:/application". Then, run the containers again (how we already created all, it will be faster):
```bash
$ sudo docker-compose up -d
```
Give the permissions to your project folder (in your terminal, not in the container terminal):
```bash
$ sudo chown <your-username>:<your-username> -R <your-project-folder>
$ sudo chmod 755 -R <your-project-folder>
```
And its done! Every change in the project folder will modify the project folder in the container. Now, you can just develop your project. To stop all containers you can just stop it with:
```bash
$ sudo docker-compose stop
```
And then, you can run it again with:
```bash
$ sudo docker-compose up -d
```

## Using docker-compose to create a React native environment
Now, lets use docker-compose to create a React native environment. For it, unzip the docker-compose file of the files folder in this repository and run it:
```bash
$ sudo docker-compose up -d
```
We are using a Ubuntu image to build the environment, installing **Nodejs** and all the packages of it. We are also installing the essentials to run andriod, like **adb**, **openjdk** and **react-native** (and some other things to make the container recognize the usb). 

Again, give the permissions to your project folder (in your terminal, not in the container terminal):
```bash
$ sudo docker exec -it react /bin/bash        # Run the terminal
$ cd application/                             # Enter the application folder
$ git clone <git-project>                     # Clone the project
$ exit                                        # Exit

# Give permissions
$ sudo chown <your-username>:<your-username> -R <your-project-folder>
$ sudo chmod 775 -R <your-project-folder>
```

### Testing the application
With the permissions, lets finish the configuration accepting the sdk terms:
```bash
$ sudo docker exec -it react /bin/bash        # Run the terminal
$ cd <your-project-folder>
$ yes | ~/android/sdk/tools/bin/sdkmanager --sdk_root=${ANDROID_HOME} --licenses
```

Install and run your project:
```bash
$ yarn install
$ yarn start
```

Now, we need to run the project in another terminal, so open a terminal in your machine and do:
```bash
$ sudo docker exec -it react /bin/bash  
$ cd <your-project-folder>
$ react-native run-android --variant=developmentDebug --appIdSuffix=development   # If this fails, run the command below
$ react-native run-android
```

## References
[Docker Compose on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-ubuntu-18-04)

[Docker Compose install and use](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04)
