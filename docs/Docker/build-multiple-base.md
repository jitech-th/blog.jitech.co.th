---
tags:
    - Docker
    - Docker build
---
# Docker build from multiple base images

## Dockerfile example

Here we want to build an image, for [seedlab](https://github.com/seed-labs/seed-labs/blob/master/lab-setup/docker-images/seed-ubuntu/medium/Dockerfile).
But beside building an image with default tools, we want to integrate `ttyd` that manual building is required.

So, as you can see, we use multiple `FROM` statements. The first one for building `ttyd` then copy `/ttyd/build/ttyd` that is built completely to `/usr/bin/ttyd` in the second one
```
FROM ubuntu:18.04
RUN apt-get update && apt-get install -y autoconf automake curl cmake git libtool make \
    && git clone --depth=1 https://github.com/tsl0922/ttyd.git /ttyd \
    && cd /ttyd && env BUILD_TARGET=x86_64 ./scripts/cross-build.sh

FROM ubuntu:20.04
ARG DEBIAN_FRONTEND=noninteractive

COPY --from=0 /ttyd/build/ttyd /usr/bin/ttyd

RUN apt-get update  \
    && apt-get -y install  \
        binutils \
        curl   \
        iproute2  \
        iputils-ping \
        vim   \
        net-tools \
        unzip \
        python3.8 \
        python3-distutils \
        openbsd-inetd \
     && rm -rf /var/lib/apt/lists/*


# Create the "seed" user account
# Change seed's and root's passwords to "dees"
RUN  useradd -m -s /bin/bash seed \
     && echo "root:dees" | chpasswd \
     && echo "seed:dees" | chpasswd

EXPOSE 7681

COPY bashrc /home/seed/.bashrc
COPY bashrc /root/.bashrc

CMD ["ttyd","bash"]
```