# Docker with Example by Aiman Rahim

## What is *Docker*?

 ![Microsoft-Docker-logo - Uncommon Solutions](https://uncommonsolutions.com/wp-content/uploads/2018/12/Microsoft-Docker-logo.png)



<p align ='justify'>
    Docker is a software platform that simplifies the process of building, running, managing and distributing applications. The main keyword for this article is <b>distributing</b>.  Docker is used to make deploying application to different computers and platform much easier without the need for additional setup. Most of the time, you just download the docker image, and run <code>`docker run --name ContainerName`</code>. In order to use a docker container to run your application in another device, that device must also have the <b>Docker Engine</b> installed
</p>



<p align="center">
    <img src="C:\Users\Aiman\Downloads\pc.png" alt="pc" style="zoom: 33%;" />
</p>
Imagine the PC icon as your own computer, with Windows installed. It is a simple machine that you use to do programming. The program that you've built is a Python application which connects to JAKIM's server to retrieve the daily prayer times. You used pip to install some of the libraries and modules needed for your program. The program has been written, you run the program, and the program returns you a beautiful table containing the daily prayer times like below.

| Salat   | Time    |
| ------- | ------- |
| Subh    | 6:06 am |
| Zuhr    | 1:18 pm |
| Asr     | 4:42 pm |
| Maghrib | 7:16 pm |
| Isya    | 8:31 pm |

 The program you wrote was excellent that you want to run it for 24 hours every single day on your PC. However, electricity is not free and not cheap like the air you breath. So you decided to install your program to a smaller, more power efficient device to save on your electricity bill like the Jetson Nano. So you started hacking on your Jetson Nano.

<p align="center">
    <img src="https://developer.nvidia.com/sites/default/files/akamai/embedded/images/jetsonNano/JetsonNano-DevKit_Front-Top_Right_trimmed.jpg" alt="Jetson Nano Developer Kit | NVIDIA Developer" style="zoom:25%;" />
</p>
After a while, reality hits you. You realized that you are not familiar with the operating system used on the Jetson Nano (Linux). Installing the Python libraries became a nightmare with error messages coming at you left and right like YouTube advertisements. You know what I mean. It's a total nightmare and nothing works. Remember, you need to have the same exact libraries/dependencies that your PC has for the program to run on the Jetson Nano. You wished there was an easier method that just works. 

**ENTER DOCKER!** 

Docker allows you to develop an application in one computer, and package all the libraries, modules, dependencies in one neat thing called a Docker Container. This allows for easier distribution and deployment of your apps. You can now run your program in your Jetson Nano easily. The Jetson Nano has to download the Docker Container that you made on your PC. Afterwards, open the Terminal and type : 

`docker run --name jakimsolatcontainer`  and presto! Your program now runs perfectly with minimal configuration.

# Getting Started

## Downloading & Installing Docker 

You can download Docker for your desktop (https://www.docker.com/)

<img src="C:\Users\Aiman\AppData\Roaming\Typora\typora-user-images\image-20230101192523732.png" alt="image-20230101192523732" style="zoom:33%;" />

Click on the Docker Installation EXE file and Install

<img src="https://www.tutorials24x7.com/uploads/2021-09-16/files/2-tutorials24x7-configure-docker-desktop-for-windows-10.png" alt="How To Install Docker Desktop On Windows 10 | Tutorials24x7" style="zoom: 50%;" />

Once finished, restart your PC.  To avoid having problems after installing, install the [WSL2 kernel update](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) from Microsoft's website

## Running Your Containers

Run the Docker Desktop application and try the tutorial!

![image-20230101192958120](C:\Users\Aiman\AppData\Roaming\Typora\typora-user-images\image-20230101192958120.png)

### Steps 

1) Open your terminal and type `docker run -d -p 80:80 docker/getting-started`
2) Open your web browser and type in the search bar `localhost:80` or just localhost to go the docker HTTP server containing the tutorial documents
3) Done! You've successfully downloaded a Docker container from the Docker Repo and running the Docker container using the `docker run` command. <img src="C:\Users\Aiman\AppData\Roaming\Typora\typora-user-images\image-20230101210822281.png" alt="image-20230101210822281" style="zoom: 80%;" />

# Building your own Docker application  

Remember that prayer time app that gets its data from JAKIM's server? Yeah, we're going distribute that.

## Testing the app

### Steps

1. Open your terminal, and type `https://github.com/PlashSpeed-Aiman/solat_email.git` and let *git* clone the code repo.
2. Open the folder `solat_email` and open the terminal. Afterwards, type `cd solat_email` and type `code . `  ![image-20230101201249422](C:\Users\Aiman\AppData\Roaming\Typora\typora-user-images\image-20230101201249422.png)

Visual Studio Code will automatically open the folder containing the app. You can try the app by first installing the needed libraries and setting up the environments. 

3. Open a new Python virtual environment on your terminal with `python -m venv env`  and afterwards run `pip install -r requirements.txt` to install all the dependencies

4.  Activate the environment by typing the command `env/Scripts/activate`

5. type `flask run` to test our app. The HTTP server will run. 

6. <p align='center'><img src="C:\Users\Aiman\AppData\Roaming\Typora\typora-user-images\image-20230101203455668.png" alt="image-20230101203455668" style="zoom: 67%;" /></p>
   To test, open your web browser and type `localhost:5000\api\SGR01`

7.  You will now receive the current daily prayer time for Gombak! 

Incase you have not noticed, we have to go through the trouble of setting up our environment and dependencies just to get the app running. This will become an inconvenience when we want to deploy to another device as we have to repeat the same process again but with the possibility of errors occurring. We don't want that, unless you're a masochist. So we will try to containerize our app with Docker

## Here Comes The Whale!

To containerized our app, first we need a dockerfile. The dockerfile is the commands and specification for Docker to create our Docker Image. It is responsible for copy our files, installing dependencies to our Docker Image.

First, create Dockerfile in your `solat_email` directory. The Dockerfile will need to be filled with some commands for Docker to process it.

```dockerfile

# syntax=docker/dockerfile:1
#using python3.8 image
FROM python:3.8-slim-buster

#our working directory which is solat_email
WORKDIR /solat_email 

#copying into our container
COPY requirement.txt requirement.txt 
# this will install our dependencies
RUN pip3 install -r requirement.txt 

# copy all files like app.py,dbInterface.py into our container. We need it lest we cannot run our application at all
COPY . . 
# Commands to run when we type docker run
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0","--port=5002"]

```

Having done that, run `docker build -t 'solat_email_docker' .` (don't miss out the dot, it's important). You will then be greeted with a string of texts like 

```shell
(env2) PS C:\Users\Aiman\Documents\solat_email> docker build -t 'solat_email_docker' .
[+] Building 44.6s (10/10) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                                                              0.1s 
 => => transferring dockerfile: 546B                                                                                                                                                              0.0s 
 => [internal] load .dockerignore                                                                                                                                                                 0.1s 
 => => transferring context: 2B                                                                                                                                                                   0.0s 
 => [internal] load metadata for docker.io/library/python:3.8-slim-buster                                                                                                                         0.7s 
 => [internal] load build context                                                                                                                                                                 1.6s 
 => => transferring context: 273.48kB                                                                                                                                                             1.4s 
 => [1/5] FROM docker.io/library/python:3.8-slim-buster@sha256:4cda66a01b5571bd4f3d634b301f72e580a94b2c1ce87ead057040a6dea4a416                                                                  25.2s 
 => => resolve docker.io/library/python:3.8-slim-buster@sha256:4cda66a01b5571bd4f3d634b301f72e580a94b2c1ce87ead057040a6dea4a416                                                                   0.1s 
 => => sha256:6ba145ad2ad6bd0be9290f841bdb5726ccfaf54a72bb3a25ea104ec031ee4655 7.53kB / 7.53kB                                                                                                    0.0s 
 => => sha256:3cf88772a5396c14aae45be5131a2e1ccba7a5850c1041de201b52b745e34cce 11.29MB / 11.29MB                                                                                                 17.6s 
 => => sha256:4cda66a01b5571bd4f3d634b301f72e580a94b2c1ce87ead057040a6dea4a416 988B / 988B                                                                                                        0.0s 
 => => sha256:e659e823e3fb173b0ff6a7e905042c18cc4eb117650f3ba74f2b69239cc8d4cc 1.37kB / 1.37kB                                                                                                    0.0s 
 => => sha256:b52ebda398ed2c4602ea06056f78d45a59474ee4e2a020967251ba082424e7e2 27.14MB / 27.14MB                                                                                                 20.4s 
 => => sha256:5ea3b77facf9f24e5cda370be6af00cfc85988f3140bee862e2529dd50e6eec1 2.78MB / 2.78MB                                                                                                    3.9s
 => => sha256:58e7c3cd0452f64ffd5e78c534d35a06366820b8e12588c506f515d266ace0dd 235B / 235B                                                                                                        4.4s 
 => => sha256:8b23ff4b1264a9a2736e18169645d7853993db56e422ccccaf22bd0bf0215b7c 3.18MB / 3.18MB                                                                                                    6.3s 
 => => extracting sha256:b52ebda398ed2c4602ea06056f78d45a59474ee4e2a020967251ba082424e7e2                                                                                                         2.5s 
 => => extracting sha256:5ea3b77facf9f24e5cda370be6af00cfc85988f3140bee862e2529dd50e6eec1                                                                                                         0.4s 
 => => extracting sha256:3cf88772a5396c14aae45be5131a2e1ccba7a5850c1041de201b52b745e34cce                                                                                                         0.8s 
 => => extracting sha256:58e7c3cd0452f64ffd5e78c534d35a06366820b8e12588c506f515d266ace0dd                                                                                                         0.0s 
 => => extracting sha256:8b23ff4b1264a9a2736e18169645d7853993db56e422ccccaf22bd0bf0215b7c                                                                                                         0.4s 
 => [2/5] WORKDIR /solat_email                                                                                                                                                                    0.9s 
 => [3/5] COPY requirement.txt requirement.txt                                                                                                                                                    0.1s 
 => [4/5] RUN pip3 install -r requirement.txt                                                                                                                                                    14.9s 
 => [5/5] COPY . .                                                                                                                                                                                1.3s 
 => exporting to image                                                                                                                                                                            1.1s 
 => => exporting layers                                                                                                                                                                           1.1s 
 => => writing image sha256:61705aa5a7d297b3753b68c9e66639270e22c8595606da1ff4e76e08e34cc865                                                                                                      0.0s 
 => => naming to docker.io/library/solat_email_docker
```

This is Docker installing your dependencies and copying files into your container. We have now built our first Docker Image. You can run the command 

`docker run -d -p 8080:5002 solat_email_docker` to start up our container!

![image-20230101214352642](C:\Users\Aiman\AppData\Roaming\Typora\typora-user-images\image-20230101214352642.png)

As you can, in order to run the app, we just run the app with the `docker run` command. There's no need for `pip install`, or activating environments. In order to fully appreciate this tutorial, try downloading the Docker Image onto another PC and run `docker run` on that PC. We have to publish the image into the Docker Hub for others to download our application. You can learn it [here](https://docs.docker.com/docker-hub/) or stay tuned for the second part of this article.



Cheers,
Aiman Rahim



# Additional Resources

[How to “Dockerize” Your Python Applications | Docker](https://www.docker.com/blog/how-to-dockerize-your-python-applications/)

[How To Build and Deploy a Flask Application Using Docker on Ubuntu 20.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-build-and-deploy-a-flask-application-using-docker-on-ubuntu-20-04)

