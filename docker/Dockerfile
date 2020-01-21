FROM ubuntu:16.04

# Modified Mitsuba Docker file from
# LABEL authors="Ben Heasly <benjamin.heasly@gmail.com>, Marceli Wac <marceliwac@gmail.com>"
LABEL authors="Felipe Gutierrez <felipegb94@gmail.com>"

### headless X server dependencies
RUN apt-get update \
    && apt-get install -y \
    libx11-dev \
    libxxf86vm-dev \
    x11-xserver-utils \
    x11proto-xf86vidmode-dev \
    x11vnc \
    xpra \
    xserver-xorg-video-dummy \
    && apt-get clean \
    && apt-get autoclean \
    && apt-get autoremove

### mitsuba dependencies
RUN apt-get update \
    && apt-get install -y \
    build-essential \
    scons \
    git \
    qt4-dev-tools \
    libpng12-dev \
    libjpeg-dev \
    libilmbase-dev \
    libxerces-c-dev \
    libboost-all-dev \
    openexr \
    libopenexr-dev \
    libglewmx-dev \
    libxxf86vm-dev \
    libpcrecpp0v5 \
    libfftw3-dev \
    wget \
    zip \
    unzip \
    p7zip-full \
    pkg-config \
    curl \
    collada-dom2.4 \
    && apt-get clean \
    && apt-get autoclean \
    && apt-get autoremove

WORKDIR /mitsuba

# libjpeg8 libjpeg8-dev
# libpng3 
# libglewmx1.6-dev 
# libxxf86vm-dev 
# libfftw3-3 
# libfftw3-dev libboost1.58-all-dev  


RUN mkdir install_dirs
RUN mkdir downloads

# Install and link eigen
RUN curl -L -o /mitsuba/downloads/eigen-3.2.10.zip http://bitbucket.org/eigen/eigen/get/3.2.10.zip
RUN cd /mitsuba/install_dirs/
RUN unzip /mitsuba/downloads/eigen-3.2.10.zip 
RUN mv eigen-eigen-* eigen_3.2.10
RUN chown -R root:root eigen_3.2.10/
RUN mv eigen_3.2.10/ /usr/local/
RUN ln -sf /usr/local/eigen_3.2.10/ /usr/local/eigen
RUN ln -sf /usr/local/eigen_3.2.10/ /usr/local/eigen3
RUN ln -sf /usr/local/eigen /usr/include/eigen3
RUN cd /mitsuba


RUN git clone https://github.com/felipegb94/MitsubaToFRenderer.git
RUN cd MitsubaToFRenderer && git fetch origin && git checkout -b develop_working-mtsimport-and-python origin/develop_working-mtsimport-and-python && cd ..
WORKDIR /mitsuba/MitsubaToFRenderer
RUN scons -j8



# ### set up Mitsuba lib and bin paths, like in Mitsuba's own setpath.sh
ENV MITSUBA_DIR /mitsuba/MitsubaToFRenderer
ENV PYTHONPATH /mitsuba/MitsubaToFRenderer/dist/python:/mitsuba/MitsubaToFRenderer/dist/python/2.7:
ENV PATH /mitsuba/MitsubaToFRenderer/wrapper:/mitsuba/MitsubaToFRenderer/dist:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV LD_LIBRARY_PATH /mitsuba/MitsubaToFRenderer/dist:

### configure headless X server
### this allows the Mitsba scene importer "mtsimport" to use OpenGL for triagle computations
COPY xorg.conf /etc/X11/xorg.conf
ENV DISPLAY :0

### wrapper to start headless xserver when using mtsimport
COPY ./mtsimport-headless.sh /mitsuba/MitsubaToFRenderer/wrapper/mtsimport


# Setup mitsuba scene dataset and mitsuba data generator
WORKDIR /mitsuba

# install pip
RUN apt-get install -y \
    python-pip \
    python-tk \
    vim \
    && apt-get clean \
    && apt-get autoclean \
    && apt-get autoremove

# We need to install specific versions of the following libraries because the latest 
# versions do not support python 2.7
# I have only tested Mitsuba's python API with 2.7, but in the future we need to test
# with more recent python versions
RUN pip install numpy==1.16.5 matplotlib==2.2.4
RUN pip install openexr
RUN pip install ipython==5.7.0

