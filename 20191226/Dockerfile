# Author: Roberto Rodriguez (@Cyb3rWard0g)
# License: GPL-3.0

# Referneces:
# https://github.com/alxchk/pupy/blob/unstable/pupy/conf/Dockerfile.default
# https://github.com/n1nj4sec/pupy/blob/unstable/pupy/conf/Dockerfile.env

FROM phusion/baseimage:0.11
LABEL maintainer="Roberto Rodriguez @Cyb3rWard0g"
LABEL description="Dockerfile Pupy."

USER root

# **** Set ARG Values ****
ARG PUPY_USER=pupy
ARG PUPY_UID=510
ARG PUPY_GID=510
ARG PUPY_HOME=/opt/pupy

ENV DEBIAN_FRONTEND noninteractive
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

# ********** Pupy User ******
ENV USER ${PUPY_USER}
ENV PUPY_UID ${PUPY_UID}
ENV HOME /home/${PUPY_USER}
ENV PUPY_GID $PUPY_GID
ENV PUPY_HOME ${PUPY_HOME}
ENV PATH ${HOME}/.local/bin:$PATH
ENV PYTHONPATH ${PYTHONPATH}:${PUPY_HOME}/pupy

  # ********** Installing Initial Requirements ***************
RUN apt-get update --fix-missing && apt-get install -y --no-install-recommends wget sudo nano \
  # ********** Adding Pupy User **************
  && echo "auth requisite pam_deny.so" >> /etc/pam.d/su \
  # remove the sudo rights for all users with admin privileges or in admin group
  && sed -i.bak -e 's/^%admin/#%admin/' /etc/sudoers \
  && sed -i.bak -e 's/^%sudo/#%sudo/' /etc/sudoers \
  && groupadd -g ${PUPY_GID} ${PUPY_USER} \
  && useradd -m -s /bin/bash -u ${PUPY_UID} -g ${PUPY_GID} ${PUPY_USER} \
  # Adding APT Keys
  && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 7638D0442B90D010 \
  && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 8B48AD6246925553 \
  # Installing dependencies
  && echo 'deb http://ftp.debian.org/debian stretch-backports main' >>/etc/apt/sources.list \
  && apt-get update \
  && mkdir -p /usr/share/man/man1/ \
  && apt-get install -t stretch-backports --no-install-recommends -y build-essential libssl1.0-dev libffi-dev \
  python-dev python-pip openssh-server sslh libcap2-bin john vim-tiny less osslsigncode nmap net-tools \
  libmagic1 swig cython autoconf automake unzip libtool locales ncurses-term bash tcpdump libpam-cap \
  netbase git fuse \
  && apt-get update \
  && apt-get -y clean autoremove \
  && rm -rf /var/lib/apt/lists/* /usr/share/doc* /usr/share/man/* /usr/share/info/* \
  # Clone Pupy Repository
  && git clone https://github.com/alxchk/pupy.git ${PUPY_HOME} \
  && cd ${PUPY_HOME} \
  && git checkout f8c829dd66449888ec3f4c7d086e607060bca892 \
  && git submodule update --init --recursive \
  && cd ${HOME} \
  # Download latest compiled payload templates
  && wget https://github.com/n1nj4sec/pupy/releases/download/latest/payload_templates.txz \
  && tar xvf payload_templates.txz \
  && mv payload_templates/* ${PUPY_HOME}/pupy/payload_templates \
  && rm payload_templates.txz \
  && rm -r payload_templates \
  # Download latest mimikatz (2020/04/14)
  && mkdir -p /opt/mimikatz \
  && wget https://github.com/gentilkiwi/mimikatz/releases/download/2.2.0-20200308/mimikatz_trunk.zip -P /opt/mimikatz/ \
  && unzip /opt/mimikatz/*.zip -d /opt/mimikatz/ \
  && rm /opt/mimikatz/*.zip \
  # tcpdump
  && chmod +s /usr/sbin/tcpdump \
  # Copying config files
  && mkdir -p ${HOME}/.config/pupy/ \
  && cp ${PUPY_HOME}/pupy/conf/pupy.conf.docker ${HOME}/.config/pupy/pupy.conf \
  && cp ${PUPY_HOME}/pupy/conf/.bashrc ${HOME}/.bashrc.pupy \
  && cp ${PUPY_HOME}/pupy/conf/capability.conf /etc/security/capability.conf \
  # Change ownership of pupy home
  && chown -R ${USER}:${USER} ${HOME} ${PUPY_HOME} /opt/mimikatz

USER ${USER}

COPY requirements.txt ${PUPY_HOME}/pupy/

# Install Py requirements
RUN python -m pip install --upgrade pip six setuptools wheel \
  && cd ${PUPY_HOME}/pupy \
  && pip install --upgrade --force-reinstall -r requirements.txt --user

USER root

RUN apt-get remove -y autoconf automake libssl1.0-dev libffi-dev python-dev libtool build-essential \
  && apt-get -y autoremove \
  && rm -rf /root/.cache/pip \
  && rm -f /tmp/requirements.txt

WORKDIR ${PUPY_HOME}/pupy
CMD ["python2.7", "pupysh.py"]

USER ${USER}