FROM debian:bullseye-slim as node

RUN dpkg --add-architecture i386 \
		&& apt-get update \
    && apt-get install -y --no-install-recommends --no-install-suggests \
        ca-certificates \
				curl

RUN curl -sL https://deb.nodesource.com/setup_19.x | bash -
RUN apt-get install -y nodejs --no-install-recommends --no-install-suggests



FROM node AS node_modules

WORKDIR /INSTALL
COPY ./watchdog/*.* ./
RUN npm install --only=production



FROM node

ENV SM_VERSION=1.11
ENV MM_VERSION=1.11

ENV APPS="csgo:740:730"
ENV KEEPCOUNT=3

RUN apt-get install -y --no-install-recommends --no-install-suggests \
			lib32gcc-s1 \
			rsync \
			lib32stdc++6

WORKDIR /steamcmd

RUN curl -sqL "https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz" | tar zxvf -
# We could prepare steamcmd, but that adds like 250 mb of stuff that will probably be re-downloaded anyways
#RUN ./steamcmd.sh +quit

# All downloaded servers will be stored here
VOLUME ["/repo"]

# Install / Config watchdog
WORKDIR /watchdog

COPY ./watchdog/*.js ./
COPY --from=node_modules /INSTALL/node_modules/ ./node_modules/

# Cleanup
# RUN apt-get remove --purge -y apt-transport-https lsb-release gnupg \
# 		&& apt-get clean autoclean \
# 		&& apt-get autoremove -y \
# 		&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN apt-get clean autoclean \
		&& apt-get autoremove -y \
		&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


ENTRYPOINT ["node", "/watchdog/main.js"]
