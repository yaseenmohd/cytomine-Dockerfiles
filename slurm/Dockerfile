#
# Copyright (c) 2009-2018. Authors: see NOTICE file.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

FROM centos:7.4.1708

RUN yum makecache fast \
    && yum -y install epel-release \
    && yum -y install \
        autoconf \
        bash-completion \
        bzip2 \
        bzip2-devel \
        file \
        gcc \
        gcc-c++ \
        gdbm-devel \
        git \
        glibc-devel \
        gmp-devel \
        libarchive-devel \
        libffi-devel \
        libGL-devel \
        libX11-devel \
        make \
        mariadb-server \
        mariadb-devel \
        munge \
        munge-devel \
        ncurses-devel \
        openssl-devel \
        openssl-libs \
        patch \
        perl \
        pkconfig \
        psmisc \
        python-devel \
        python-pip \
        python34 \
        python34-devel \
        python34-pip \
        readline-devel \
        sqlite-devel \
        tcl-devel \
        tix-devel \
        tk \
        tk-devel \
        supervisor \
        wget \
        vim-enhanced \
        zlib-devel \
    && yum -y install https://repo.ius.io/ius-release-el7.rpm \
    && yum -y install \
        python35u \
        python35u-devel \
        python35u-pip \
        python36u \
        python36u-devel \
        python36u-pip \
    && yum clean all \
    && rm -rf /var/cache/yum

RUN yum install -y openssh-server openssh-clients passwd net-tools
RUN ssh-keygen -q -N "" -t dsa -f /etc/ssh/ssh_host_dsa_key && ssh-keygen -q -N "" -t rsa -f /etc/ssh/ssh_host_rsa_key
RUN sed -ri 's/UsePAM yes/UsePAM no/g' /etc/ssh/sshd_config && echo 'root:root' | chpasswd

RUN pip2.7 install Cython nose \
    && pip3.4 install Cython nose \
    && pip3.6 install Cython nose

ARG SLURM_VERSION=20.11.8
ARG SLURM_DOWNLOAD_MD5=cbfe4f64fef41e2e2b5cd6622bea9bfd
ARG SLURM_DOWNLOAD_URL=https://download.schedmd.com/slurm/slurm-"$SLURM_VERSION".tar.bz2

RUN set -x \
    && wget -O slurm.tar.bz2 "$SLURM_DOWNLOAD_URL" \
    && echo "$SLURM_DOWNLOAD_MD5" slurm.tar.bz2 | md5sum -c - \
    && mkdir /usr/local/src/slurm \
    && tar jxf slurm.tar.bz2 -C /usr/local/src/slurm --strip-components=1 \
    && rm slurm.tar.bz2 \
    && cd /usr/local/src/slurm \
    && ./configure --enable-debug --enable-front-end --prefix=/usr \
       --sysconfdir=/etc/slurm --with-mysql_config=/usr/bin \
       --libdir=/usr/lib64 \
    && make install \
    && install -D -m644 etc/cgroup.conf.example /etc/slurm/cgroup.conf.example \
    && install -D -m644 etc/slurm.conf.example /etc/slurm/slurm.conf.example \
    && install -D -m644 etc/slurmdbd.conf.example /etc/slurm/slurmdbd.conf.example \
    && install -D -m644 contribs/slurm_completion_help/slurm_completion.sh /etc/profile.d/slurm_completion.sh \
    && cd \
    && rm -rf /usr/local/src/slurm \
    && groupadd -r slurm  \
    && useradd -r -g slurm slurm \
    && mkdir /etc/sysconfig/slurm \
        /var/spool/slurmd \
        /var/run/slurmd \
        /var/lib/slurmd \
        /var/log/slurm \
    && chown slurm:root /var/spool/slurmd \
        /var/run/slurmd \
        /var/lib/slurmd \
        /var/log/slurm \
    && /sbin/create-munge-key

RUN yum -y install squashfs-tools


ARG VERSION=1.14.15
ARG OS=linux
ARG ARCH=amd64
ARG GOPATH=${HOME}/go
RUN cd /tmp && wget https://go.dev/dl/go$VERSION.linux-amd64.tar.gz && tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz


ENV PATH=/usr/local/go/bin:${PATH}:${GOPATH}/bin

RUN go get -u github.com/golang/dep/cmd/dep

ARG SINGULARITY_VERSION=3.7.3
# Singularity installation
RUN mkdir -p $GOPATH/src/github.com/apptainer && \
    cd $GOPATH/src/github.com/apptainer && \
    wget https://github.com/apptainer/singularity/releases/download/v$SINGULARITY_VERSION/singularity-$SINGULARITY_VERSION.tar.gz && \
    tar xvf singularity-$SINGULARITY_VERSION.tar.gz && \
    cd singularity && \
    ./mconfig

RUN cd $GOPATH/src/github.com/apptainer/singularity/builddir && \
    make && \
    make install

RUN adduser cytomine && \
    echo "cytomine:cytomine" | chpasswd

RUN mkdir -p /home/cytomine/.ssh
RUN mkdir -p /data/softwares/images
RUN chmod -R 777 /data/softwares/images

COPY slurm.conf /etc/slurm/slurm.conf
COPY slurmdbd.conf /etc/slurm/slurmdbd.conf
RUN chmod 600 /etc/slurm/slurmdbd.conf && chown slurm /etc/slurm/slurmdbd.conf

COPY supervisord.conf /etc/

VOLUME ["/var/lib/mysql", "/var/lib/slurmd", "/var/spool/slurmd", "/var/log/slurm"]

COPY deploy.sh /usr/local/bin/deploy.sh
RUN chmod +x /usr/local/bin/deploy.sh

EXPOSE 22 80

ENTRYPOINT ["/usr/local/bin/deploy.sh"]
CMD ["/bin/bash"]
