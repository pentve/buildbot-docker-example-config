# buildbot/buildbot-worker

# please follow docker best practices
# https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/

# Provides a base Ubuntu (16.04) image with latest buildbot worker installed

FROM        ubuntu:16.04
MAINTAINER  mariadb.com

USER root

COPY . /usr/src/buildbot-worker
COPY docker/buildbot.tac /buildbot/buildbot.tac

# Last build date - this can be updated whenever there are security updates so
# that everything is rebuilt
ENV         security_updates_as_of 2017-06-14

# This will make apt-get install without question
ARG         DEBIAN_FRONTEND=noninteractive

# Install security updates and required packages
RUN         apt-get update && \
            apt-get -y upgrade && \
            apt-get -y install -q \
                build-essential \
                git \
                python-dev \
                libffi-dev \
                libssl-dev \
                curl && \
            rm -rf /var/lib/apt/lists/* && \

# Get the latest pip (newer than python-pip; old versions have security issues)
            curl -Lo get-pip.py https://bootstrap.pypa.io/get-pip.py && \
            python get-pip.py && \

# Test runs produce a great quantity of dead grandchild processes.  In a
# non-docker environment, these are automatically reaped by init (process 1),
# so we need to simulate that here.  See https://github.com/Yelp/dumb-init
            curl -Lo /usr/local/bin/dumb-init https://github.com/Yelp/dumb-init/releases/download/v1.1.3/dumb-init_1.1.3_amd64 && \
            chmod +x /usr/local/bin/dumb-init && \

			pip install virtualenv && \
            pip --no-cache-dir install twisted[tls] && \
            pip install /usr/src/buildbot-worker && \

    useradd -ms /bin/bash buildbot && chown -R buildbot /buildbot

USER buildbot

WORKDIR /buildbot

CMD ["/usr/local/bin/dumb-init", "twistd", "-ny", "buildbot.tac"]

USER root

RUN apt-get update && apt-get install -y cmake build-essential dpkg-dev devscripts hardening-wrapper autoconf automake autotools-dev binutils bison chrpath debhelper doxygen dpatch dvipdfmx fakeroot fontconfig-config g++ gawk gcc gettext ghostscript ghostscript-x gsfonts html2text intltool-debian libc6-dev libcroco3 libcups2 libcupsimage2 libfontconfig1 libfontenc1 libfreetype6 libgomp1 libice6 libjpeg62 libltdl7 libltdl7-dev libmail-sendmail-perl libncurses5-dev libpaper-utils libpaper1 libpng12-0 libsm6 libsys-hostname-long-perl libtool libwrap0-dev libxaw7 libxfont1 libxmu6 libxpm4 libxt6 linux-libc-dev lmodern m4 make patchutils po-debconf tex-common texlive-base texlive-base-bin texlive-latex-base texlive-latex-base-doc ttf-dejavu ttf-dejavu-core ttf-dejavu-extra xfonts-encodings xfonts-utils zlib1g-dev

# libstdc++6-4.7-dev
# libreadline6-dev


