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
    * [Ubuntu](https://hub.docker.com/_/ubuntu)
    * [debian](https://hub.docker.com/_/debian)
    * [CentOS](https://hub.docker.com/_/centos)
    * [Alpine](https://hub.docker.com/_/alpine)
    * [Windows](https://hub.docker.com/_/microsoft-windows-base-os-images)
* Base Software Docker Containers (This list is not comprehensive.)
    * [R](https://hub.docker.com/_/r-base)
    * [Python](https://hub.docker.com/_/python)
    * [Miniconda](https://hub.docker.com/r/continuumio/miniconda3)
    * [Jupyter](https://hub.docker.com/u/jupyter)
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
> If you are not knowledgeable on how to use the RIS Compute Platform, you can go over the following documentation.
>
> [Compute2 Quickstart](https://washu.atlassian.net/wiki/spaces/RUD/pages/2304147627/Quickstart+Guides#cfm-tab-1-2) 

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

## Example of Using the Compute2 Platform for Development

* As mentioned before you can develop a Docker image on the Compute Platform(s).
* The first thing you’ll need to do is load the required modules in order to build containers.
* For this demonstration we will be using Podman which will build a Docker container.

```
module load ris
module load podman
```

* Once you have this set, you can start development of your Docker image.
* For this simple example, we’ll use the vi text editor that’s available on the Compute Platform to edit our Dockerfile.
* It is also suggested that you work out of a Storage directory for most images. For this example, it is ok to work from your home directory.
* You need to create a directory called docker-example and then cd into that directory.
* Once in the docker-example directory, you will need to create your Dockerfile via the following command

```
vi Dockerfile
```

* Once in the editor you will need to press the `I` key to be able to edit the file. Now you just need to put in the information in the Dockerfile like developed above.
* Once you’ve entered the info, then you press the escape key to disengage edit mode.
* Then you need to press the `:` key and type `wq` and hit the Enter key. This will write your changes and quit the editor.
* Now you can use the cat command on your file to make sure it contains the correct information.
* The next step will be to create a directory that holds the VM and container data the Podman uses.
* It is recommended to do this in either a scratch or storage location.

```
mkdir -p /storageN/fs1/allocation_name/podman/runtime
```
* Then we will need to start up an interactive job with variables that are set to the directory that we created.

```
XDG_CONFIG_HOME=/storageN/fs1/allocation_name/podman \         # defualt: $HOME/.config 
XDG_DATA_HOME=/storageN/fs1/allocation_name/podman \           # default: $HOME/.local/share 
XDG_RUNTIME_DIR=/storageN/fs1/allocation_name/podman/runtime \ # default: /run/user/$UID 
srun -A compute2-group -p general-interactive --pty /bin/bash
```

* Now that we have an interactive job, we can work to build our Docker image.
* We will need to initiate and start up the virtual machine.

```
podman machine init
podman machine start
```

> [!NOTE]
> This can take a bit of time to get started.

* You should see output similar to the following.

```
Starting machine "podman-machine-default"

This machine is currently configured in rootless mode. If your containers
require root permissions (e.g. ports < 1024), or if you run into compatibility
issues with non-podman clients, you can switch using the following command:

	podman machine set --rootful

Mounting volume... /home/elyn:/home/elyn
API forwarding listening on: /storage2/fs1/elyn/Active/podman/runtime/podman/podman-machine-default-api.sock
You can connect Docker API clients by setting DOCKER_HOST using the
following command in your terminal session:

        export DOCKER_HOST='unix:///storage2/fs1/elyn/Active/podman/runtime/podman/podman-machine-default-api.sock'

Machine "podman-machine-default" started successfully
```

* Next we run the build command.

```
podman build -t docker-example .
```

* If we are not in the directory of our Dockerfile or it is not named one of the valid "default" names, we will need to add the `-f` option.
* Default names:
   * Dockerfile
   * Containerfile

```
podman build -t docker-example -f ./Dockerfile .
```

* The following is an example of the output when running the build command.

```
STEP 1/4: FROM ubuntu:jammy
STEP 2/4: RUN apt-get update && apt-get install -y --no-install-recommends cowsay fortune fortunes && apt-get clean
Get:1 http://archive.ubuntu.com/ubuntu jammy InRelease [270 kB]
Get:2 http://security.ubuntu.com/ubuntu jammy-security InRelease [129 kB]
Get:3 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [128 kB]
Get:4 http://archive.ubuntu.com/ubuntu jammy-backports InRelease [127 kB]
Get:5 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [3637 kB]
Get:6 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [6205 kB]
Get:7 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [1289 kB]
Get:8 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [60.9 kB]
Ign:9 http://archive.ubuntu.com/ubuntu jammy/restricted amd64 Packages
Get:10 http://archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [17.5 MB]
Get:11 http://archive.ubuntu.com/ubuntu jammy/multiverse amd64 Packages [266 kB]
Get:12 http://archive.ubuntu.com/ubuntu jammy/main amd64 Packages [1792 kB]
Get:13 http://archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [6411 kB]
Get:14 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1600 kB]
Get:15 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [3968 kB]
Get:16 http://archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 Packages [69.2 kB]
Get:17 http://archive.ubuntu.com/ubuntu jammy-backports/universe amd64 Packages [37.2 kB]
Get:18 http://archive.ubuntu.com/ubuntu jammy-backports/main amd64 Packages [83.9 kB]
Get:9 http://archive.ubuntu.com/ubuntu jammy/restricted amd64 Packages [164 kB]
Fetched 43.7 MB in 1min 19s (550 kB/s)
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
The following additional packages will be installed:
  fortunes-min libgdbm-compat4 libgdbm6 libperl5.34 librecode0
  libtext-charwidth-perl perl perl-modules-5.34
Suggested packages:
  filters cowsay-off x11-utils bsdmainutils gdbm-l10n perl-doc
  libterm-readline-gnu-perl | libterm-readline-perl-perl make
  libtap-harness-archive-perl
Recommended packages:
  netbase
The following NEW packages will be installed:
  cowsay fortune-mod fortunes fortunes-min libgdbm-compat4 libgdbm6
  libperl5.34 librecode0 libtext-charwidth-perl perl perl-modules-5.34
0 upgraded, 11 newly installed, 0 to remove and 4 not upgraded.
Need to get 9670 kB of archives.
After this operation, 53.1 MB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 perl-modules-5.34 all 5.34.0-3ubuntu1.5 [2977 kB]
Get:2 http://archive.ubuntu.com/ubuntu jammy/main amd64 libgdbm6 amd64 1.23-1 [33.9 kB]
Get:3 http://archive.ubuntu.com/ubuntu jammy/main amd64 libgdbm-compat4 amd64 1.23-1 [6606 B]
Get:4 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libperl5.34 amd64 5.34.0-3ubuntu1.5 [4797 kB]
Get:5 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 perl amd64 5.34.0-3ubuntu1.5 [232 kB]
Get:6 http://archive.ubuntu.com/ubuntu jammy/main amd64 libtext-charwidth-perl amd64 0.04-10build3 [10.2 kB]
Get:7 http://archive.ubuntu.com/ubuntu jammy/universe amd64 cowsay all 3.03+dfsg2-8 [18.6 kB]
Get:8 http://archive.ubuntu.com/ubuntu jammy/main amd64 librecode0 amd64 3.6-24build2 [670 kB]
Get:9 http://archive.ubuntu.com/ubuntu jammy/universe amd64 fortune-mod amd64 1:1.99.1-7.1 [36.9 kB]
Get:10 http://archive.ubuntu.com/ubuntu jammy/universe amd64 fortunes-min all 1:1.99.1-7.1 [55.1 kB]
Get:11 http://archive.ubuntu.com/ubuntu jammy/universe amd64 fortunes all 1:1.99.1-7.1 [833 kB]
debconf: delaying package configuration, since apt-utils is not installed
Fetched 9670 kB in 3s (3368 kB/s)
Selecting previously unselected package perl-modules-5.34.
(Reading database ... 4393 files and directories currently installed.)
Preparing to unpack .../00-perl-modules-5.34_5.34.0-3ubuntu1.5_all.deb ...
Unpacking perl-modules-5.34 (5.34.0-3ubuntu1.5) ...
Selecting previously unselected package libgdbm6:amd64.
Preparing to unpack .../01-libgdbm6_1.23-1_amd64.deb ...
Unpacking libgdbm6:amd64 (1.23-1) ...
Selecting previously unselected package libgdbm-compat4:amd64.
Preparing to unpack .../02-libgdbm-compat4_1.23-1_amd64.deb ...
Unpacking libgdbm-compat4:amd64 (1.23-1) ...
Selecting previously unselected package libperl5.34:amd64.
Preparing to unpack .../03-libperl5.34_5.34.0-3ubuntu1.5_amd64.deb ...
Unpacking libperl5.34:amd64 (5.34.0-3ubuntu1.5) ...
Selecting previously unselected package perl.
Preparing to unpack .../04-perl_5.34.0-3ubuntu1.5_amd64.deb ...
Unpacking perl (5.34.0-3ubuntu1.5) ...
Selecting previously unselected package libtext-charwidth-perl.
Preparing to unpack .../05-libtext-charwidth-perl_0.04-10build3_amd64.deb ...
Unpacking libtext-charwidth-perl (0.04-10build3) ...
Selecting previously unselected package cowsay.
Preparing to unpack .../06-cowsay_3.03+dfsg2-8_all.deb ...
Unpacking cowsay (3.03+dfsg2-8) ...
Selecting previously unselected package librecode0:amd64.
Preparing to unpack .../07-librecode0_3.6-24build2_amd64.deb ...
Unpacking librecode0:amd64 (3.6-24build2) ...
Selecting previously unselected package fortune-mod.
Preparing to unpack .../08-fortune-mod_1%3a1.99.1-7.1_amd64.deb ...
Unpacking fortune-mod (1:1.99.1-7.1) ...
Selecting previously unselected package fortunes-min.
Preparing to unpack .../09-fortunes-min_1%3a1.99.1-7.1_all.deb ...
Unpacking fortunes-min (1:1.99.1-7.1) ...
Selecting previously unselected package fortunes.
Preparing to unpack .../10-fortunes_1%3a1.99.1-7.1_all.deb ...
Unpacking fortunes (1:1.99.1-7.1) ...
Setting up librecode0:amd64 (3.6-24build2) ...
Setting up libtext-charwidth-perl (0.04-10build3) ...
Setting up perl-modules-5.34 (5.34.0-3ubuntu1.5) ...
Setting up fortunes-min (1:1.99.1-7.1) ...
Setting up fortune-mod (1:1.99.1-7.1) ...
Setting up libgdbm6:amd64 (1.23-1) ...
Setting up fortunes (1:1.99.1-7.1) ...
Setting up libgdbm-compat4:amd64 (1.23-1) ...
Setting up libperl5.34:amd64 (5.34.0-3ubuntu1.5) ...
Setting up perl (5.34.0-3ubuntu1.5) ...
Setting up cowsay (3.03+dfsg2-8) ...
Processing triggers for libc-bin (2.35-0ubuntu3.11) ...
--> da39e4f5ee6d
STEP 3/4: ENV PATH="$PATH:/usr/games"
--> 4ef925889f34
STEP 4/4: RUN export PATH
COMMIT docker-example
--> aebe8da4c386
Successfully tagged localhost/docker-example:latest
aebe8da4c386225132dd67effdd691beb3abe2c99981944ddf75a05fef374199
```

* We can test this directly in the interactive job we are running.

```
podman run -it --rm docker-example cowsay "Hello World"
 _____________
< Hello World >
 -------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

* Now that we have our Docker image created, we can push it to Docker Hub.
* In order to do that, we will need to login to docker. That can be done with the following command.

```
podman login docker.io
```

* You will need to enter your Docker Hub login credentials.
* Next we will need to tag the image for the docker repository. For our particular example that would look like the following.

```
podmnan tag docker-example docker.io/username/docker-example
```

* Once this is done, we can push the image to Docker Hub.

```
podman push docker.io/username/docker-example
```

* You should see something like looks likes the following when you do.

```
Getting image source signatures
Copying blob sha256:767e56ba346ae714b6e6b816baa839051145ed78cfa0e4524a86cc287b0c4b00
Copying blob sha256:df6db609d21c4c698d480f6cb69bf79a945195856c1499e6d7ddfc593207f2cc
Copying blob sha256:f75a61801f77e8df2b6b9778a814fca5edbcba989fd8f4ff94254c132628c4e7
Copying config sha256:aebe8da4c386225132dd67effdd691beb3abe2c99981944ddf75a05fef374199
Writing manifest to image destination
```

* Now the image can be used from Docker Hub just like we have already covered.
