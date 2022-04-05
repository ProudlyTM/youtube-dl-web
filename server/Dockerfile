# new base (python:3.9-slim->ubuntu:latest)
FROM ubuntu:latest

# install required packages
# & create the static web folder
RUN apt update \
 && DEBIAN_FRONTEND=noninteractive apt install -y sudo python3.9 python3-pip nginx ffmpeg \
 \
 && mkdir -p /etc/ytdl-web/static

# copy entrypoint script to root
COPY entrypoint.sh /entrypoint.sh

# replace the nginx config
COPY ./nginx.conf /etc/nginx/nginx.conf

# install deps as root,
# create the nonroot user "user", group "user" and add the user "user" to the sudoers group
# & make the entrypoint script executable
COPY ./requirements.txt /tmp/requirements.txt
RUN pip3  --disable-pip-version-check --no-cache-dir install -r /tmp/requirements.txt \
 && rm -v /tmp/requirements.txt \
 && pip3 install -U yt-dlp \
 \
 && groupadd user \
 && useradd user -g sudo -m -s /usr/sbin/nologin \
 && echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers \
 \
 && chmod 555 /entrypoint.sh

# copy the source code
# & fix ownership of stuff inside application folder
WORKDIR /home/user/app
COPY --chown=user:user ./src ./

# expose ports 80 and 443 for nginx
EXPOSE 80 443

# switch to user "user"
USER user

# run the entrypoint script to start nginx as root and run uvicorn as nonroot
CMD [ "/entrypoint.sh" ]