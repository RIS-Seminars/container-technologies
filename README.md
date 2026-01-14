## What will this documentation provide?

* An introduction to using container technologies.
* An example of utilizing Docker to create an example container.
* Leveraging Docker images to do analysis on the RIS Compute Platform(s).

## What is needed?

* Access to the RIS Compute2 Platform.
* Knowledge of how to submit jobs on the RIS Compute2 Platform.
* Docker installed on your local computer or ability to utilize Docker development on Compute2.
* An account on Docker Hub.

## What is Docker?

* The technical definition of Docker is an open-source project that automates the deployment of software applications inside containers by providing an additional layer of abstraction and automation of OS-level virtualization on Linux.
* The simpler definition is that Docker is a tool that allows users to deploy applications within a sandbox (containers) to run on the host operating system (RIS Compute Platform).
* This method allows for all dependencies and environments to be able to remain unique to the software without interacting or interfering with other software’s environments.

## What is a container?

* A container is a virtual machine (VM), that runs software applications.
* This means that a container is virtual computer that has an operating system (OS) and whatever software users installed.
* Docker has created a syntax language for creating these virtual computers which get referred to then as Docker containers.
* You can find more information about why RIS chose Docker and containers [here.](https://washu.atlassian.net/wiki/spaces/RUD/pages/1705115761/Docker+and+the+RIS+Compute1+Platform?atlOrigin=eyJpIjoiNTdkZGE2ZGU0MTYwNDA5NmJhNzM2ODY4OGU1ZGYzOTYiLCJwIjoiYyJ9)

## Where can I find Docker?

* You can find official Docker documentation [here.](https://docs.docker.com/)
* You can find Docker containers and a place to host Docker containers [here.](https://hub.docker.com/)
* You can download Docker [here.](https://www.docker.com/products/docker-desktop)

## Creating a Docker Container

### 1. Decide The Base Container

* The first thing you’ll want to when creating a docker container, is decide what type of container you want to start with as the base.
* You can start with a base container of just an operating system, like Ubuntu, or you can start with a container that already has software installed.
* Basic OS Docker Containers (This list is not comprehensive.)
    * Ubuntu
    * debian
    * CentOS
    * Alpine
    * Windows
* Base Software Docker Containers (This list is not comprehensive.)
    * R
    * Python
    * Miniconda
    * Jupyter
* For our example we are going to start with the `jammy` Ubuntu image.
* To do that we need to open up a text editor and create the base of our container.
```
#Start from bionic base Ubuntu image.
FROM ubuntu:jammy
```

### 2. Install Software and Software Dependencies

* The next step is to determine what software we’re going to install.
* For this example we’ll be installing a software called cowsay.
* To do this, we’ll have to use apt-get to install the software.
* First we’ll want to do an update using the following command.
```
RUN apt-get update
```
* Then we need to actually install cowsay. In the install, since we’re installing in a Docker image, we’ll want to use some options to make it cleaner.
* The command should look like the following.
```
RUN apt-get install -y --no-install-recommends cowsay
```
* Once all of the software we want to install has been installed, we will want to run a clean to help keep our image clean and smaller.
```
RUN apt-get clean
```
* We can run all the apt-get commands with the same RUN command if we wish, by utilizing `&&`. Now our Dockerfile should look like the following.
```
RUN apt-get update \
    && apt-get install -y --no-install-recommends cowsay \
    && apt-get clean
```
* We next will need to add the directory where cowsay is installed to the PATH variable so that we can use the software.
```
ENV PATH="$PATH:/usr/games"
RUN export PATH
```
* Now our Dockerfile should look like the following.
```
#Start from bionic base Ubuntu image.
FROM ubuntu:bionic

#Install cowsay
RUN apt-get update \
    && apt-get install -y --no-install-recommends cowsay \
    && apt-get clean

#Add cowsay to the PATH variable
ENV PATH="$PATH:/usr/games"
RUN export PATH
```

### 3. Build, Test, and Upload An Image

* Once you have your Dockerfile saved within a directory (folder) designed for the image, the next step is to build the container.
* The Docker base command to build a Docker container from a Dockerfile, looks like the following.
```
docker build -t username/container-name:tag directory
```
* In our case, we’ll be using a directory named docker-example and we’ll simply call the container `docker-example`.
* `username` refers to your Docker Hub username.
* So, our Docker build command should look like the following.
```
docker build -t username/docker-example:latest docker-example/
```
* Or if we are within the directory for the Docker image it should look like the following.
```
docker build -t username/docker-example:latest .
```
> [!Note]
> If you are using a Mac that has the newer Mac chips, you will need to add the following option to your build command.
>
> `--platform linux/amd64`
* If it builds successfully, you should get output of information about the building process, but at the end you’ll see the following.
```
[+] Building 14.3s (8/8) FINISHED                                                                                                                                                                                                 docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                                                              0.0s
 => => transferring dockerfile: 314B                                                                                                                                                                                                              0.0s
 => [internal] load metadata for docker.io/library/ubuntu:jammy                                                                                                                                                                                   1.9s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                                                                     0.0s
 => [internal] load .dockerignore                                                                                                                                                                                                                 0.0s
 => => transferring context: 2B                                                                                                                                                                                                                   0.0s
 => [1/3] FROM docker.io/library/ubuntu:jammy@sha256:104ae83764a5119017b8e8d6218fa0832b09df65aae7d5a6de29a85d813da2fb                                                                                                                             2.8s
 => => resolve docker.io/library/ubuntu:jammy@sha256:104ae83764a5119017b8e8d6218fa0832b09df65aae7d5a6de29a85d813da2fb                                                                                                                             0.0s
 => => sha256:0ec3d86457676c7af7a3b6d29565e0e8b30ed98afe5d606e00e565101f812623 27.38MB / 27.38MB                                                                                                                                                  2.5s
 => => extracting sha256:0ec3d86457676c7af7a3b6d29565e0e8b30ed98afe5d606e00e565101f812623                                                                                                                                                         0.3s
 => [2/3] RUN apt-get update     && apt-get install -y --no-install-recommends cowsay fortune fortunes lolcat     && apt-get clean                                                                                                                6.1s
 => [3/3] RUN export PATH                                                                                                                                                                                                                         0.1s 
 => exporting to image                                                                                                                                                                                                                            3.2s 
 => => exporting layers                                                                                                                                                                                                                           2.6s 
 => => exporting manifest sha256:6ada80510c155c3d18fcd83aed701243066c2230d485433614fec9d8f5eec3e5                                                                                                                                                 0.0s 
 => => exporting config sha256:049730cd7aabeabe314872b2f852373136f77a1193ee722b615ab42787b94ef7                                                                                                                                                   0.0s 
 => => exporting attestation manifest sha256:83ac6ae135efff31264682eb37335ec113c97d114eacb864898152467244e6f7                                                                                                                                     0.0s 
 => => exporting manifest list sha256:e0df366baddc2b3beed504b390ef44b5cf2745de5f3ea02b36cae58433bddeb6                                                                                                                                            0.0s
 => => naming to docker.io/elynrfw/docker-example:latest                                                                                                                                                                                          0.0s
 => => unpacking to docker.io/elynrfw/docker-example:latest                                                                                                                                                                                       0.6s
```
* Now we can run the Docker image we’ve created.
* The base Docker run command is as follows.
```
docker run username/container-name:tag command
```
* For our example image, this will look like the following.
```
docker run username/docker-example:latest cowsay "Hello World"
```
* Your output should look like the following.
```
 _____________
< Hello World >
 -------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```
* Once we are certain our Docker image is functioning correctly, we can then push it to Docker Hub.
* The basic push command looks as follows.
```
docker push username/container-name:tag
```
* For our example the command should look like the following.
```
docker push username/docker-example:latest
```
* You should see output like the following for the push.
```
% docker push elynrfw/docker-example:latest
The push refers to repository [docker.io/elynrfw/docker-example]
53021aadb317: Pushed 
4f4fb700ef54: Pushed 
0ec3d8645767: Pushed 
44cea8a09479: Pushed 
latest: digest: sha256:e0df366baddc2b3beed504b390ef44b5cf2745de5f3ea02b36cae58433bddeb6 size: 855
```

### 4. Expanding An Image

* While it’s fun to tell our cow what to say, what if we had it say randomly generated fortunes?
* We can do this by also installing the fortune library into our docker container.
* Luckily, once our base image has been designed this requires changing only 1 line in our Dockerfile.
* We will add fortune and fortunes to our `apt-get install` command, like the following.
```
&& apt-get install -y --no-install-recommends cowsay fortune fortunes \
```
* Once that is changed, we can save the Dockerfile and rebuild the image.
* Now we can pipe fortune into cowsay and create our fortune telling cow.
* Unfortunately when running Docker, to be able to use the pipe command we need to add /bin/bash -c to our command.
* So our new Docker run command should look like the following.
```
docker run username/docker-example:latest /bin/bash -c "fortune | cowsay"
```
* If everything is working correctly, we should get output like the following.
```
 ________________________________________
/ The way to love anything is to realize \
\ that it might be lost.                 /
 ----------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```
* You can re-upload your image to Docker Hub so that you have the newest image available for the next part.
* Your complete Dockerfile should now look like the following.
```
#Start from jammy base Ubuntu image.
FROM ubuntu:jammy

#Install cowsay
RUN apt-get update \
&& apt-get install -y --no-install-recommends cowsay fortune fortunes \
&& apt-get clean

#Add cowsay to the PATH variable
ENV PATH="$PATH:/usr/games"
RUN export PATH
```

## Using a Docker Container on the Compute2 Platform

* Now that we have our docker container created and uploaded to Docker Hub, we can use it to run the software we installed on the RIS Compute2 Platform.
> [!NOTE]
> If you are not knowledgeable on how to use the RIS Compute Platform, you can go over the following documentation. [Compute2 Quickstart](https://washu.atlassian.net/wiki/spaces/RUD/pages/2304147627/Quickstart+Guides#cfm-tab-1-2) 
* To get the output we want on the RIS Compute Platform, we will have to use the following commands.
```
srun -A compute2-group -p general-interactive --container-image="username/docker-example:latest" --pty /bin/bash -c "fortune | cowsay"
```
* Again, in this case the username is your Docker Hub username.
* `compute2-group` is the Compute2 Account or Group that you are part of.
* If everything is working correctly, you should see results like the following.
```
srun: job 204631 queued and waiting for resources
srun: job 204631 has been allocated resources
pyxis: importing docker image: elynrfw/docker-example:latest
pyxis: imported docker image: elynrfw/docker-example:latest
 ________________________________________
/ The way to love anything is to realize \
\ that it might be lost.                 /
 ----------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```
* `cowsay` has a fun little additional aspect. If a fortune telling cow isn’t fantastic enough, we can always use a dragon.
* If you add the `-f dragon` option to the cowsay command, you can turn your cow into a dragon.
```
srun -A compute2-group -p general-interactive --container-image="username/docker-example:latest" --pty /bin/bash -c "fortune | cowsay -f dragon"
srun: job 204633 queued and waiting for resources
srun: job 204633 has been allocated resources
pyxis: importing docker image: elynrfw/docker-example:latest
pyxis: imported docker image: elynrfw/docker-example:latest
 ________________________________________
/ The way to love anything is to realize \
\ that it might be lost.                 /
 ----------------------------------------
      \                    / \  //\
       \    |\___/|      /   \//  \\
            /0  0  \__  /    //  | \ \    
           /     /  \/_/    //   |  \  \  
           @_^_@'/   \/_   //    |   \   \ 
           //_^_/     \/_ //     |    \    \
        ( //) |        \///      |     \     \
      ( / /) _|_ /   )  //       |      \     _\
    ( // /) '/,_ _ _/  ( ; -.    |    _ _\.-~        .-~~~^-.
  (( / / )) ,-{        _      `-.|.-~-.           .~         `.
 (( // / ))  '/\      /                 ~-. _ .-~      .-~^-.  \
 (( /// ))      `.   {            }                   /      \  \
  (( / ))     .----~-.\        \-'                 .~         \  `. \^-.
             ///.----..>        \             _ -~             `.  ^-`  ^-_
               ///-._ _ _ _ _ _ _}^ - - - - ~                     ~-- ,.-~
                                                                  /.-~
```
## Advanced Container Addition

As an advanced step, we can add another library that will add color to our cows and dragons.

lolcat adds rainbow coloring to the display.

Once we add it to our container, update, and upload to Docker Hub, we can add the color via lolcat with the following command.

There are no hints for this step as it is advanced and designed for the user to figure out.

export LSF_DOCKER_PRESERVE_ENVIRONMENT=false
bsub -Is -q workshop-interactive -G compute-workshop -a 'docker(username/docker-example:latest)' /bin/bash -c "fortune | cowsay | lolcat"

## Example of Using the Compute2 Platform for Development

As mentioned before you can develop a Docker image on the Compute Platform.

The first thing you’ll need to do is let Docker Hub know your credentials by using the following command.

LSB_DOCKER_LOGIN_ONLY=1 bsub -G compute-workshop -q workshop-interactive -Is -a 'docker_build' -- .

This command only needs to be done once and will ask for your Docker Hub credentials (you’ll need an Docker Hub account prior to this step).

The login credentials you’ll need to use are those for Docker Hub NOT your WashU credentials.

Once you have this set, you can start development of your Docker image.

If you wish to use the Compute Platform to develop images or other software, we suggest checking out our documentation on doing so (Coming Soon!).

For this simple example, we’ll use the vi text editor that’s available on the Compute Platform.

You need to create a directory called docker-example and then cd into that directory.

Once in the docker-example directory, you will need to create your Dockerfile via the following command

vi Dockerfile

Once in the editor you will need to press the I key to be able to edit the file. Now you just need to put in the information in the Dockerfile like developed above.

Once you’ve entered the info, then you press the escape key to disengage edit mode.

Then you need to press the : key and type wq and hit the Enter key. This will write your changes and quit the editor.

Now you can use the cat command on your file to make sure it contains the correct information.

In order to build and push our Docker image, we need to cd back out of docker-example directory.

Then we need to use the following command to build and push our Docker image.

bsub -G compute-workshop -q workshop-interactive -Is -a 'docker_build(username/docker-example)' -- latest docker-example

Where username is your Docker Hub username.

Once this is entered you will see the normal Docker building information. 

Now that the container is built and pushed, we can use the container like we need above in order to have our fortune telling cow.

export LSF_DOCKER_PRESERVE_ENVIRONMENT=false
bsub -Is -q workshop-interactive -G compute-workshop -a 'docker(username/docker-example:latest)' /bin/bash -c "fortune | cowsay"


