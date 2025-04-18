FROM aaii/cuda:9.2-cudnn7-devel-ubuntu18.04
#FROM nvidia/cuda:12.2.0-devel-ubuntu22.04

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
# Python Version x.y
ARG PYTHON_VERSION=3.6
ARG PYLUCENE_VERSION=7.4.0


RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
        git \
		unzip \
		p7zip-full \
        wget \
        cmake \
        ninja-build \
        build-essential \
		checkinstall \
		software-properties-common \
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* 

	
RUN add-apt-repository ppa:deadsnakes/ppa

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
        "python$PYTHON_VERSION-dev" \
		"python$PYTHON_VERSION-distutils" \
		python3-setuptools \
        python3-pip \
        "python$PYTHON_VERSION-venv" \
		libstdc++6 \
		gcc-multilib\
		libc6 \
		 && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* 
	
	
	
# ======================== START OF ADDITIONAL INSTALLATION ========================

# Install Java
RUN apt-get update && \
	apt-get install -y --no-install-recommends \
    build-essential \
    ant \
    curl \
    git \
    default-jdk
  



RUN ln -sf $(which python$PYTHON_VERSION) /usr/bin/python

#RUN python --version

RUN python -m pip install --upgrade pip

#RUN which python$PYTHON_VERSION && which python && python --version

WORKDIR /usr/lib/jvm/default-java/jre/lib
RUN ln -s ../../lib amd64

# Java 11
RUN java --version && javac --version


#RUN wget https://dlcdn.apache.org/maven/maven-3/3.8.8/binaries/apache-maven-3.8.8-bin.tar.gz
#RUN tar -xvf apache-maven-3.8.8-bin.tar.gz
#RUN cp -r apache-maven-3.8.8 /opt
#RUN ln -s /opt/apache-maven-3.8.8 /opt/maven
#RUN export PATH=/opt/apache-maven-3.8.8/bin:$PATH
#RUN echo -e "\nPATH=\"/opt/apache-maven-3.8.8/bin:\$PATH\"" >> ~/.profile

RUN apt install maven -y


RUN mvn -version

#RUN rm apache-maven-3.8.8-bin.tar.gz


# Installing PyLucene
RUN which ant && ant -version

RUN apt-get install -y --no-install-recommends \
    libffi-dev \
    zlib1g-dev

WORKDIR /usr/src/pylucene
RUN curl https://archive.apache.org/dist/lucene/pylucene/pylucene-$PYLUCENE_VERSION-src.tar.gz | tar -xz



ENV PREFIX_PYTHON=/usr \
    JCC_JDK=/usr/lib/jvm/default-java \
    ANT=ant \
    JCC='python -m jcc' \
    NUM_FILES=10 \
    PYTHON=python \
    NO_SHARED=1
	




RUN mkdir -p "/root/.ant/lib"

WORKDIR /root/.ant/lib

COPY ivy-2.4.0.jar .

WORKDIR /usr/src/pylucene

RUN cd "pylucene-$PYLUCENE_VERSION/lucene-java-$PYLUCENE_VERSION/lucene" && \
    ant ivy-bootstrap && \
    ant && \
    cd ../../../

RUN cd "pylucene-$PYLUCENE_VERSION/jcc" && \
    ls -la && \
    NO_SHARED=1 JCC_JDK=/usr/lib/jvm/default-java python setup.py build && \
    NO_SHARED=1 JCC_JDK=/usr/lib/jvm/default-java python setup.py install && \
    cd .. && \
    make JCC="python -m jcc" ANT=ant PYTHON=python NUM_FILES=8&& \
    make install JCC="python -m jcc" ANT=ant PYTHON=python NUM_FILES=8 && \
    cd ../../


WORKDIR /usr/src
RUN rm -rf pylucene

WORKDIR /app
COPY . .

WORKDIR /


RUN python -m pip install --upgrade pip \
    && python -m venv /opt/python/venv/base

COPY requirements.txt /opt/python/venv/base/


#RUN /opt/python3/venv/base/bin/python3 -m pip install torch==1.9.0+cu111 torchvision==0.10.0+cu111 torchaudio==0.9.0 -f https://download.pytorch.org/whl/torch_stable.html
RUN /opt/python/venv/base/bin/python -m pip install --upgrade pip
RUN /opt/python/venv/base/bin/python -m pip install --no-cache-dir -r /opt/python/venv/base/requirements.txt
RUN /opt/python/venv/base/bin/python -m pip install --force-reinstall torch==1.4.0+cu92 torchvision==0.5.0+cu92 -f https://download.pytorch.org/whl/torch_stable.html



COPY GTrans-main .



COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# Set entrypoint to bash
ENTRYPOINT ["/entrypoint.sh"]