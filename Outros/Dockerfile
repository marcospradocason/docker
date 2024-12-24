FROM --platform=linux/amd64 ubuntu:plucky AS linux_image

RUN apt update && apt install -y locales && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

ENV LANG="en_US.utf8"
ENV DEBIAN_FRONTEND="noninteractive"
ENV TZ="America/Sao_Paulo"
ENV OPENCTI_ADMIN_EMAIL="admin@opencti.io"
ENV OPENCTI_ADMIN_PASSWORD="ChangeMePlease"
ENV OPENCTI_ADMIN_TOKEN="${cat /proc/sys/kernel/random/uuid}"
ENV OPENCTI_BASE_URL="http://localhost:8080"
ENV OPENCTI_ADMIN_EMAIL="admin@opencti.io"
ENV OPENCTI_ADMIN_PASSWORD="ChangeMe"
ENV OPENCTI_ADMIN_TOKEN="ChangeMe_UUIDv4"
ENV OPENCTI_BASE_URL="http://localhost:8080"
ENV OPENCTI_HEALTHCHECK_ACCESS_KEY="ChangeMe"

ARG RELEASE_VERSION="6.4.5"

RUN <<EOF

set -x \

&& apt update \
&& apt full-upgrade -y \
&& apt dist-upgrade -y \
&& apt autoremove -y \
&& apt autoclean -y \
&& apt install tzdata wget curl lsb-release nano sudo build-essential python3-full nodejs \
npm python3 python3-pip python3-dev python3-virtualenv python3-pip python3-wheel python3-xyzservices -y \

&& mkdir /opencti \
&& cd /opencti \
&& wget https://github.com/OpenCTI-Platform/opencti/releases/download/${RELEASE_VERSION}/opencti-release-${RELEASE_VERSION}.tar.gz\
--no-check-certificate \
&& tar xvfz opencti-release-${RELEASE_VERSION}.tar.gz \

&& cd opencti \
&& cp config/default.json config/production.json \

&& cd src/python \
&& python3 -m venv .venv \
&& source .venv/bin/activate \
&& pip3 install -r requirements.txt \

&& cd ../.. \
&& npm install --global yarn \
&& yarn config set --home enableTelemetry 0 \
&& yarn install \
&& yarn build \
&& yarn serv \

&& cd worker \
&& apt install libmagic-dev -y
&& pip3 install -r requirements.txt \
&& cp config.yml.sample config.yml \
&& python3 worker.py & \

EOF

CMD ["/bin/bash"]

# Acesso ao opencti > http://localhost:4000/
# wget https://github.com/OpenCTI-Platform/opencti/releases/download/6.4.5/opencti-release-6.4.5.tar.gz