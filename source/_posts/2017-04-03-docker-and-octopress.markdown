---
layout: post
title: "Docker and Octopress"
date: 2017-04-04 18:41:51 +0000
comments: true
categories: [docker, octopress, linux]
---
This post describes how I created my first customized docker image(s).

I have been watching the [docker](https://www.docker.com/) space for a while and finally found a private use-case: This blog uses [Octopress](http://octopress.org/), which is a ruby-based convenience-wrapper around Jekyll. 
[Jekyll](https://jekyllrb.com/) is a static web-site generator provided by [GitHub](https://github.com/jekyll/jekyll). 
Octopress requires some old libs: Ruby 1.9.3, Python 2.7, and nodejs.

So, to use Octopress on any machine, I have to either:

- configure the machine to use specific versions of Ruby, Python and NodeJs. Works.
    - Drawback: Other projects using different versions of Ruby, Python, NodeJs won't work out of the box.
- use version managers for Ruby, Python and NodeJs (f.ex. [`rvm`](https://rvm.io/), [`virtualenv`](http://fgimian.github.io/blog/2012/12/08/setting-up-virtual-development-environments-for-python/), [`nvm`](https://github.com/creationix/nvm)). Works. 
    - Drawback: Tedious setup which differs between OSes.
- use a virtual machine. Works. 
    - Drawback: Not easily portable due to size of virtual machine image.
- Or, I could use docker.

I decided to give docker a spin.

**My primary goal was to be able to blog from any (linux) machine running docker.**

From a birds-eye view my goal is to:

- install a docker image on any machine 
- and run a docker container with my blog mounted as shared folder (so I can edit the content on the host system, but compilation, preview and publishing is accomplished from within the docker container)

**My secondary goal was to get my hands dirty with docker :-)**

Obviously docker also has potential usage for other development setups (i.e. testing application code in local docker container before pushing to CI to reduce roundtrip time).



## Overview

I created 3 docker images, which build upon each other:

- Docker 00: base image including Ruby, Python and NodeJs
- Docker 01: image with docker `ENTRYPOINT`
- Docker 02: image optimized for octopress usage

Here is the folder structure:

```sh
├── 00_ruby_base
│   ├── build-image.sh
│   └── Dockerfile
├── 01_user
│   ├── build-image.sh
│   ├── Dockerfile
│   └── entrypoint.sh
├── 02_octopress
│   ├── build-image.sh
│   ├── Dockerfile
│   ├── post-install.sh
│   └── run-container.sh
└── share
    └── octopress
```

Each image (`00*`, `01*`, `02*`) contains a `Dockerfile` and a `build-image.sh` file. Only the last image (`02*`) contains a `run-container.sh` file.

- `Dockerfile`s contain the instructions for building a docker image.
- `build-image.sh` files invoke the `Dockerfile`.

## Docker 00: base image

Since I couldn't find a simple Ruby image of 1.9.3 at docker hub I decided to create my own.

Knowing my use-case (Octopress), I also installed Python2.7 and NodeJs for my [base docker image](https://hub.docker.com/r/draptik/ruby1.9.3-python2.7-nodejs/). This image is the only one that takes quite some time to build.

### Dockerfile

```sh Dockerfile
FROM debian:jessie

# Get the dependencies for Octopress page generation
##
## Notes:
##
## - Python 2.7 is required for using pygments gem.
## - NodeJs is required for execjs Gem
##
RUN apt-get update && \
    apt-get --no-install-recommends -y install \
    autoconf \
    bison \
    build-essential \
    libssl-dev \
    libyaml-dev \
    locales \
    libreadline6-dev \
    zlib1g-dev \
    libncurses5-dev \
    libffi-dev \
    libgdbm3 \
    libgdbm-dev \
    nodejs \
    python2.7 \
    wget \
    ca-certificates \
    curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Set LOCALE to UTF8
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen en_US.UTF-8 && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    /usr/sbin/update-locale LANG=en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LC_ALL en_US.UTF-8

# Install ruby (adopted from https://hub.docker.com/r/liaisonintl/ruby-1.9.3/~/dockerfile/)
ENV RUBY_MAJOR=1.9 \
    RUBY_VERSION=1.9.3-p551 \
    RUBY_DOWNLOAD_SHA256=bb5be55cd1f49c95bb05b6f587701376b53d310eb1bb7c76fbd445a1c75b51e8 \
    RUBYGEMS_VERSION=2.6.6 \
    PATH=/usr/local/bundle/bin:$PATH
RUN set -ex && \
    curl -SL -o ruby.tar.gz "http://cache.ruby-lang.org/pub/ruby/$RUBY_MAJOR/ruby-$RUBY_VERSION.tar.gz" && \
    echo "$RUBY_DOWNLOAD_SHA256 ruby.tar.gz" | sha256sum -c - && \
    mkdir -p /usr/src/ruby && \
    tar -xzf ruby.tar.gz -C /usr/src/ruby --strip-components=1 && \
    rm -f ruby.tar.gz && \
    cd /usr/src/ruby && \
    autoconf && \
    ./configure --disable-install-doc --sysconfdir=/etc/ && \
    make && \
    make install && \
    gem update --system $RUBYGEMS_VERSION && \
    rm -rf /usr/src/ruby

# Create soft link for python
RUN ln -s /usr/bin/python2.7 /usr/bin/python
```

Here is a short description of what happens in this `Dockerfile`:

```sh
RUN apt-get ...
```
...retrieves required packages from the debian package repository.


```sh
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen ...
```
...ensures the default system language uses UTF8 (required by some packages).

```sh
RUN set -ex && curl... && make ...
```
...downloads, compiles and installs Ruby from scratch (this step takes some time!).

```sh
RUN ln -s /usr/bin/python2.7 /usr/bin/python
```
...creates a soft link to Python2.7.

### Docker build

To execute the previous `Dockerfile`, run `./build-image.sh`.

```sh build-image.sh
#!/bin/bash
docker build -t draptik/ruby1.9.3-python2.7-nodejs:0.1 .
```

Make the file executable (`chmod 744 build-image.sh`).

Ensure to replace `draptik` with some other string (f.ex. your name, initials or company) to build your own image. F.ex. `docker build -t homersimpson/ruby1.9.3-python2.7-nodejs:0.1 .`

**Since this image is going to be the base image for the next step, ensure to always use the same name (f.ex. `homersimpson`)**. 

You can verify that the docker build step worked as expected by listing all docker images using `docker images`. The output should be similar to:

```sh
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
homersimpson/ruby1.9.3-python2.7-nodejs:0.1   0.1                 641ca1a59e87        8 days ago          486 MB
debian              jessie              e5599115b6a6        4 weeks ago         123 MB
```

## Docker 01: user

Here is where things start getting difficult. Sharing a folder from the host system with docker. And keeking permissions/users in sync... 

### Some things to know about sharing a volume in docker

Sharing data between host and docker container is normally accomplished by `docker run -v host-location/folder:container-location/folder`. 

Be aware, though:

- The volume will be owned by the container
- The container's default user is root (UID/GID 1)!
- The container will change the UID/GID on the host system!

### My workaround

I found this [post by Deni Bertovic](https://denibertovic.com/posts/handling-permissions-with-docker-volumes/). In short, the post proposes to use docker's `ENTRYPOINT` to pipe all `RUN` commands through the `ENTRYPOINT`. Which in turn is a bash script (`entrypoint.sh`, see below), creating a new user, and executing all docker commands as user. This is where I start walking on very thin ice... Nevertheless, I created another [docker image](https://hub.docker.com/r/draptik/ruby1.9.3-python2.7-nodejs-user/) based on the base image from the previous step.

### Dockerfile

Make sure to replace `draptik` in the `FROM` string...

```sh Dockerfile
FROM draptik/ruby1.9.3-python2.7-nodejs:0.1

# For details see https://denibertovic.com/posts/handling-permissions-with-docker-volumes/

RUN apt-get update && apt-get -y --no-install-recommends install \
    ca-certificates \
    curl

RUN gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4
RUN curl -o /usr/local/bin/gosu -SL "https://github.com/tianon/gosu/releases/download/1.10/gosu-$(dpkg --print-architecture)" \
    && curl -o /usr/local/bin/gosu.asc -SL "https://github.com/tianon/gosu/releases/download/1.10/gosu-$(dpkg --print-architecture).asc" \
    && gpg --verify /usr/local/bin/gosu.asc \
    && rm /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu

COPY entrypoint.sh /usr/local/bin/entrypoint.sh

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
```

For further details about the above `Dockerfile` refer to aforementioned [post by Deni](https://denibertovic.com/posts/handling-permissions-with-docker-volumes/).

The `entrypoint.sh` file should be located beside the `Dockerfile`:

```sh entrypoint.sh
#!/bin/bash

# Add local user
# Either use the LOCAL_USER_ID if passed in at runtime or
# fallback

USER_ID=${LOCAL_USER_ID:-9001}

echo "Starting with UID : $USER_ID"
useradd --shell /bin/bash -u $USER_ID -o -c "" -m user
export HOME=/home/user

exec /usr/local/bin/gosu user "$@"
```

### Docker build

...and the corresponding `docker build` command (again, wrapped in a file):

```sh build-image.sh
#!/bin/bash
docker build -t draptik/ruby1.9.3-python2.7-nodejs-user:0.1 .
```

...again, make sure to replace `draptik`...

## Docker 02: octopress

Because, in addition to mounting the content of my blog, I also mount the blog-engine itself (using `docker run -v <orig-location>:<container-location>`) 
I also have to execute an initial script within the mounted folder to setup the blog-engine. To prepare the environment for this script I create a customized `~/.gemrc` and `~/.bashrc` file in the `Dockerfile`.
For this purpose I mount another file from the `docker run` script (`post-install.sh`), which must be executed from within the container.

### Dockerfile

(Make sure to replace `draptik` in the `FROM` string...)

```sh Dockerfile
FROM draptik/ruby1.9.3-python2.7-nodejs-user:0.1

# I am not really sure why this is needed, because we have an ENTRYPOINT in the parent image.
RUN useradd -ms /bin/bash user

# Setup ruby/bundler to work with non-admin user
RUN echo "gem: --user-install" > /home/user/.gemrc && chown user:user /home/user/.gemrc
RUN echo "PATH=\"/home/user/.gem/ruby/1.9.1/bin:$PATH\"" >> /home/user/.bashrc && chown user:user /home/user/.bashrc

WORKDIR /octopress
```

You might be wondering why I am explicitly creating a new user (`RUN useradd -ms /bin/bash user`). Valid question. In the next 2 lines I write some config values to files which are located in the `/home/user/` folder. I was not able to do this without first explicitly creating the user. Probably not best practice, but it works. I would be very grateful for feedback on this issue.

### Docker build

(...again, make sure to replace `draptik`...)

```sh build-image.sh
#!/bin/bash
docker build -t draptik/octopress:0.1 .
```

### Starting the final image as docker container

The following script starts the docker container:

```sh run-container.sh
#!/bin/bash
docker run \
    --rm \
    -it \
    -e LOCAL_USER_ID=`id -u $USER` \
    -p 4001:4001 \
    -v ${PWD}/../share/octopress:/octopress \
    -v ${PWD}/post-install.sh:/home/user/post-install.sh \
    draptik/octopress:0.1 \
    /bin/bash
```

Some notes about the `docker run` options:

- `--rm` ensures that the docker container is removed once exited
- `-it` runs an interactive terminal as soon as the container starts
- `-e LOCAL_USER...` sets the host user's ID within the docker container
- `-p ...` maps the port numbers
- `-v ${PWD}/../share/octopress:/octopress` mounts the blog volume
- `-v ${PWD}/post-install.sh:/home/user/post-install.sh` mounts the post install script

Mounting volumes in docker using `docker-machine` or `Docker for Windows` on windows requires some extra path-tweaking. I intend to add these tweaks in the future...

#### post-install

Yet another step... After docker mounted the external volumes from the host, they have to be configured (including our blogging engine).

That is the reason for the `post-install.sh` script. It must be run from within the container!

IMPORTANT: Ensure to replace the git user name/email in `post-install.sh`. Otherwhise you will not be able to deploy!

```sh post-install.sh
#!/bin/bash
#
# This script must be executed in ~ folder (not in /octopress)!
gem install --no-ri --no-rdoc \
	bundler \
	execjs

cd /octopress
# Important: use `--path...`!
bundle install --path $HOME/.gem

git config --global user.name "FirstName LastName"
git config --global user.email "your@mail.com"
```


## Final usage for Octopress users

All images are published to [docker hub](https://hub.docker.com/u/draptik/).

Just run `docker pull draptik/octopress:0.1`.

- Create a folder for your blog: `mkdir blog && cd blog`
- Create a folder for the blog content: `mkdir share && cd share`

Initially clone octopress in `share` folder:

```sh
git clone -b source <octopress-git-repo> octopress
cd octopress
git clone <octopress-git-repo> _deploy
```

Then, run the `run-container.sh` script.

From within the newly created docker container, follow the steps from the post-install section above.

You should now be able to use Octopress from within the docker container (i.e. `rake new_post["test"]`, `rake generate`, `rake preview`, `rake deploy`, etc.) while being able to edit your posts on the host machine.

## Summary

It helps if you have a linux background, since all docker images are linux based. Setting up a customized docker image can be a bit tedious (especially configuring user privileges and mounting host folders), but once the image works you have an automated and reproducable environment. I think this makes it worth the effort. 

Obviously I am just starting with docker, so take my example above with a grain of salt. But maybe the example gives you a starting point for your own docker experiments.

As always: Thankful for feedback!

## Sources at Github

You can find the complete source code at Github here: [https://github.com/draptik/octopress-docker](https://github.com/draptik/octopress-docker)