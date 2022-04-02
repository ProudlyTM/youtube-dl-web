# new base (python:3.9-slim->ubuntu:latest)
FROM ubuntu:latest

# install required packages
RUN apt update \
 && apt install -y software-properties-common \
 && add-apt-repository -y ppa:deadsnakes/ppa \
 && apt install -y sudo python3.9 python3-pip nginx ffmpeg

# create the static web folder and replace the nginx config
RUN mkdir -p /etc/ytdl-web/static
COPY ./nginx.conf /etc/nginx/nginx.conf

# install deps as root
COPY ./requirements.txt /tmp/requirements.txt
RUN pip3  --disable-pip-version-check --no-cache-dir install -r /tmp/requirements.txt
RUN rm -v /tmp/requirements.txt
RUN pip3 install -U yt-dlp

# create the nonroot user "user" and add it to the sudoers group
RUN useradd user -m -s /usr/sbin/nologin \
 && adduser user sudo \
 && echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# copy the source code
WORKDIR /home/user/app
COPY ./src ./

# fix ownership of stuff inside application folder
RUN chown -R user:user .

# copy entrypoint script to root and make it executable
COPY entrypoint.sh /entrypoint.sh
RUN chmod 555 /entrypoint.sh

# expose ports 80 and 443 for nginx
EXPOSE 80 443

# switch to user "user"
USER user

# run the entrypoint script to start nginx as root and run uvicorn as nonroot
CMD [ "/entrypoint.sh" ]