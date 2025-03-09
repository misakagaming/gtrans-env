FROM aaii/cuda:9.2-cudnn7-devel-ubuntu18.04
#FROM nvidia/cuda:12.2.0-devel-ubuntu22.04

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
# Python Version x.y
ARG PYTHON_VERSION=3.6
ARG PYLUCENE_VERSION=7.4.0


RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
        git \
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
	

RUN python3 -m pip install --upgrade pip \
    && python3 -m venv /opt/python3/venv/base
	
	
# ======================== START OF ADDITIONAL INSTALLATION ========================

# Install Java
RUN apt-get update && \
	apt-get install -y --no-install-recommends \
    build-essential \
    ant \
    curl \
    git \
    default-jdk
  



#RUN ln -sf $(which python$PYTHON_VERSION) /usr/bin/python

#RUN python --version

#RUN python -m pip install --upgrade pip

#RUN which python$PYTHON_VERSION && which python && python --version

WORKDIR /usr/lib/jvm/default-java/jre/lib
RUN ln -s ../../lib amd64

# Java 11
RUN java --version && javac --version

WORKDIR /app
COPY . .

WORKDIR /


COPY requirements.txt /opt/python3/venv/base/


#RUN /opt/python3/venv/base/bin/python3 -m pip install torch==1.9.0+cu111 torchvision==0.10.0+cu111 torchaudio==0.9.0 -f https://download.pytorch.org/whl/torch_stable.html
RUN /opt/python3/venv/base/bin/python3 -m pip install --upgrade pip
RUN /opt/python3/venv/base/bin/python3 -m pip install --no-cache-dir -r /opt/python3/venv/base/requirements.txt
RUN /opt/python3/venv/base/bin/python3 -m pip install --force-reinstall torch==1.4.0+cu92 torchvision==0.5.0+cu92 -f https://download.pytorch.org/whl/torch_stable.html



COPY GTrans-main .




COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# Set entrypoint to bash
ENTRYPOINT ["/entrypoint.sh"]