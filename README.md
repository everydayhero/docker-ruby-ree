# Ruby Enterprise Edition

- It's based upon a trusted build [here](https://registry.hub.docker.com/u/kneip/ree-1.8.7-2012.02/).
- This build is in-turn based upon the phusion-baseimage 0.9.9 image
- The additional apt-packages are the same as those present on the buildpacks:jessie repo, to give us a fighting chance of installing gems.

> Cool, but what's it for?

Some of our older apps are currently in production on REE. This is the first step to getting them onto a densely-utilised fleet of docker hosts.

Add the following Dockerfile in the root of any legacy project to get started. The next steps will be about serving traffic (via unicorn, etc) and logging to STDOUT / STDERR.

```
FROM everydayhero/ruby-ree
MAINTAINER Dan Sowter "daniels@everydayhero.com.au"

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
ADD Gemfile /usr/src/app/
ADD Gemfile.lock /usr/src/app/
RUN bundle install

ADD . /usr/src/app

CMD ["/bin/bash"]
```

Using this image just for deploys (via capistrano)? Don't forget to give the container access to your host's SSH agent.

```sh
docker run -rm -t -i -v $(dirname $SSH_AUTH_SOCK):$(dirname $SSH_AUTH_SOCK) -e SSH_AUTH_SOCK=$SSH_AUTH_SOCK everydayhero/ruby-ree /bin/bash
```
