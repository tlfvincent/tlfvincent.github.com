---
layout:     post
title: "Data science with Docker"
date:       2016-04-30
author:     "Thomas Vincent"
header-img: "img/galaxy.jpg"
---

<style>
.center-image
{
    margin: 0 auto;
    display: block;
}
</style>

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>



___

## Using docker to facilitate your data science pipelines

Until recently, and like many other fellow data scientists I have talked to, I built data science pipelines on my local machine or a remote host while relying on virtual environments. In doing so, I ensured some degree of replicability by keeping check of language versions, library versions, and so on. While this is a fairly efficient process that facilitated shared coding and portability, I do occasionally run into a common, recurring error.

For better or for worse, much of my development is performed on my local machine, and on the occasion where datasets or RAM requirements are too large, I will rely on a DigitalOcean droplet or EC2 instance to resolve that problem. However, neither of these cases tend to be the final step, as I will often refactor/modify my code in order to productionalize it into something that won't terrify the code delivery team. In many cases, this will involve transitioning to a different machine or OS, such as moving from MAC OS to Ubuntu 14.04, and this is where the pain points with virtual environments appear. Often I will find myself configuring the OS to my requirements, installing the correct Python version, gcc compilers and BLAS libraries, which invariably leads to minor (sometimes major) conflicts, bugs and annoyances. More importantly, there is no way to really persist all these efforts should you wish to run this on another machine or have someone reproduce your code independently.

Docker solves all these issues!

### What does Docker do?

Paraphrasing from their website,

```
Docker containers wrap up a piece of software in a complete filesystem that contains everything it needs to run: code, runtime, system tools, system libraries – anything you can install on a server. This guarantees that it will always run the same, regardless of the environment it is running in.
```

In other words, Docker offers the ability to `contain` ALL your code and dependencies into a single `container`, and never have to worry about it ever again (actually those were exactly the same words, but less well-written...). This was an intriguing concept, and the more qualified engineers around me assured me that it was also a `good` concept, so I decided to integrate this as part of my regular workflow. In the following, I will go through the steps to get started and create your first Docker container for a data science project, and hopefully convince you that Docker should be integrated as part of your regular workflow along the way!

If you haven't already, you can install Docker on your local machine using the instructions at the following link [https://docs.docker.com/engine/installation/mac/](https://docs.docker.com/engine/installation/mac/). Once that is done, you can go ahead and find the Docker Quickstart Terminal icon, double-click on it, and see a new terminal window appear with the following output:

{% highlight sh %}
Last login: Tue Feb 16 21:43:51 on ttys005
bash --login '/Applications/Docker/Docker Quickstart Terminal.app/Contents/Resources/Scripts/start.sh'
TLV-private:~ ThomasVincent$ bash --login '/Applications/Docker/Docker Quickstart Terminal.app/Contents/Resources/Scripts/start.sh'
Machine default already exists in VirtualBox.
Starting machine default...
(default) Starting VM...
Started machines may have new IP addresses. You may need to re-run the `docker-machine env` command.

Regenerate TLS machine certs?  Warning: this is irreversible. (y/n): Regenerating TLS certificates
Detecting the provisioner...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Setting environment variables for machine default...
{% endhighlight %}

                        ##         .
                  ## ## ##        ==
               ## ## ## ## ##    ===
           /"""""""""""""""""\___/ ===
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~
           \______ o           __/
             \    \         __/
              \____\_______/

{% highlight sh %}
docker is configured to use the default machine with IP XXX.XXX.XX.XXX
For help getting started, check out the docs at https://docs.docker.com

TLV-private:~ ThomasVincent$
{% endhighlight %}

Now that Docker is up and running, we can begin to build the main components of our container. For now, we will assume that you have a Python script that you want to automatically run every time you run a Docker container (although as we will see, other use cases can easily be adapted).

### The basics

The very simplistic workflow for creating your own Docker container is the following:

* Create a file called `Dockerfile`, which we will use to place the relevant instructions and commands that will define what our container can do.
* Build the Docker container, this will install and apply all the environnment variables and configurations that you have specified in the Dockerfile.
* Run the Dockerfile and reap the benefits of fully reproducible research across any computing platform!


#### Writing your own Dockerfile
A Dockerfile is usually started by specifying a base image from which you would like to start. Here, we will use the Ubuntu 14.04 image. It is also customary to specify the maintainer/creator of the Docker container you are about to create.

{% highlight sh %}
# specifiy base image
FROM ubuntu:14.04

# provide creator/maintainer of this Dockerfile
MAINTAINER Thomas Vincent <your_email_address@something.com>
{% endhighlight %}

The ubuntu:14.04 image comes as a very barebones implementation of the Linux OS, so it is up to us to specify some of the useful system tools and libraries that we would like to include:

{% highlight sh %}
# Update the sources list
RUN apt-get update

# install useful system tools and libraries
RUN apt-get install -y libfreetype6-dev && \
    apt-get install -y libglib2.0-0 \
                       libxext6 \
                       libsm6 \
                       libxrender1 \
                       libblas-dev \
                       liblapack-dev \
                       gfortran \
                       libfontconfig1 --fix-missing

RUN apt-get install tar \
                    git \
                    curl \
                    nano \
                    wget \
                    dialog \
                    net-tools \
                    build-essential
{% endhighlight %}

At this point, we are in a good position to also install the Python and the _pip_ Python package manager.

{% highlight sh %}
# install Python and pip package manager
RUN apt-get install -y python \
                       python-dev \
                       python-distribute \
                       python-pip
{% endhighlight %}

As _pip_ is installed, we can now go ahead and install commonly used Python libraries and other more specific ones that may be required by your Python script or web app.

{% highlight sh %}
# intall useful and/or required Python libraries to run your script
RUN pip install matplotlib \
                seaborn \
                pandas \
                numpy \
                scipy \
                sklearn \
                python-dateutil \
                gensim
{% endhighlight %}

Once you are sure that all the required Python libraries have been added, you can go ahead and define the command that you would like to when your Docker conatiner starts. In this case, we specify to run your Python script.

{% highlight sh %}
# define command to when Docker container starts
ENTRYPOINT ["python"]
CMD ["my_script.py"]
{% endhighlight %}

In all, your Dockerfile should look like this

{% highlight sh %}
# specifiy base image
FROM ubuntu:14.04

# provide creator/maintainer of this Dockerfile
MAINTAINER Thomas Vincent <your_email_address@something.com>

# Update the sources list
RUN apt-get update

# install useful system tools and libraries
RUN apt-get install -y libfreetype6-dev && \
    apt-get install -y libglib2.0-0 \
                       libxext6 \
                       libsm6 \
                       libxrender1 \
                       libblas-dev \
                       liblapack-dev \
                       gfortran \
                       libfontconfig1 --fix-missing

RUN apt-get install tar \
                    git \
                    curl \
                    nano \
                    wget \
                    dialog \
                    net-tools \
                    build-essential

# install Python and pip package manager
RUN apt-get install -y python \
                       python-dev \
                       python-distribute \
                       python-pip

# intall useful and/or required Python libraries to run your script
RUN pip install matplotlib \
                seaborn \
                pandas \
                numpy \
                scipy \
                sklearn \
                python-dateutil \
                gensim

# define command to when Docker container starts
ENTRYPOINT ["python"]
CMD ["my_script.py"]
{% endhighlight %}

Considering how much Docker can simplify your life, this is a pretty small and straighforward amount of code to have to write!

### Building and running your Docker container
Now that you have written your own Dockerfile, you can go ahead and build the entire image. If you haven't already, follow the instructions at the beginning of this post to ensure that you are running from within the Docker Terminal. Be sure that you are also working from within the directory in which your Dockerfile and Python script are located. You can then build your first Docker image by simply typing:

{% highlight sh %}
docker build -t your_image_name .
{% endhighlight %}

The `-t` flag allows you to name the docker container as whatever pleases you. When pressing enter, you should see a fair amount of logging information and other miscallaneous output displayed to your console. Once the build is finished, you can then go straight ahead and run your Docker image, which will compute or output whatever your Python script was intended to do!

{% highlight sh %}
docker run -t your_image_name
{% endhighlight %}


### Shutting down your docker container
Once you are done with your docker container, you can shut it down by using the command

{% highlight sh %}
docker rm -f CONTAINER_ID
{% endhighlight %}

where the CONTAINER_ID value can be found by running 

{% highlight sh %}
docker ps
{% endhighlight %}


### Conclusions
We have covered a simple use case for Docker that allowed us to produce a running instance of a Python script that - along with all of its required dependencies - can be seamlessly ran across any system. In many ways, we have only scratched the surface of what can be done with Docker, but hopefully this demonstrates how useful it can be (and that is why it has seen an explosive growth in adoption across all segments of the tech industry!)
