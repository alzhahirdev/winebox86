FROM ubuntu:20.04

LABEL maintainer="alzhahir"

ENV DEBIAN_FRONTEND noninteractive

## add container user
RUN useradd -m -d /home/container container
ENV USER=container HOME=/home/container

## install dependencies
RUN apt update && apt upgrade -y
RUN apt install -y gnupg2 tzdata software-properties-common libntlm0 winbind xvfb xauth python3 wget ca-certificates locales apt-transport-https

## add armhf architecture
RUN dpkg --add-architecture armhf

## add ryanfortner's box86 repo
RUN wget https://ryanfortner.github.io/box86-debs/box86.list -O /etc/apt/sources.list.d/box86.list
RUN wget -O- https://ryanfortner.github.io/box86-debs/KEY.gpg | gpg --dearmor |  tee /usr/share/keyrings/box86-debs-archive-keyring.gpg

RUN apt update

## install dependencies and box86 itself
RUN apt install libasound2 libc6 libc6:armhf libglib2.0-0 libgphoto2-6 libgphoto2-port12 \
        libgstreamer-plugins-base1.0-0 libgstreamer1.0-0 libldap-2.4-2 libopenal1 libpcap0.8 libpulse0 \
        libsane1 libudev1 libunwind8 libusb-1.0-0 libvkd3d1 libx11-6 libxext6 ocl-icd-libopencl1 \
        libasound2-plugins libncurses6 libtinfo5 libasound2:armhf libc6:armhf libglib2.0-0:armhf \
        libgphoto2-6:armhf libgphoto2-port12:armhf libgstreamer-plugins-base1.0-0:armhf libgstreamer1.0-0:armhf \
        libldap-2.4-2:armhf libopenal1:armhf libpcap0.8:armhf libpulse0:armhf libsane1:armhf libudev1:armhf \
        libunwind8:armhf libusb-1.0-0:armhf libvkd3d1:armhf libx11-6:armhf libxext6:armhf ocl-icd-libopencl1:armhf \
        libasound2-plugins:armhf libncurses6:armhf libmpeg2-4:armhf libmpeg2encpp-2.1-0:armhf libtinfo6:armhf \
        libmpg123-0:armhf libtinfo5:armhf cabextract libxslt1.1:armhf zenity box86 -y

## add x86 architecture to prepare for wine
RUN dpkg --add-architecture i386

## arch-qualify the current repositories
RUN sed -i "s/deb h/deb [arch=arm64] h/g" /etc/apt/sources.list

## add i386's repos
RUN echo "# i386 repositories" >> /etc/apt/sources.list
RUN echo "deb [arch=i386] http://archive.ubuntu.com/ubuntu/ focal main restricted" >> /etc/apt/sources.list
RUN echo "deb [arch=i386] http://archive.ubuntu.com/ubuntu/ focal-updates main restricted" >> /etc/apt/sources.list
RUN echo "deb [arch=i386] http://archive.ubuntu.com/ubuntu/ focal universe" >> /etc/apt/sources.list
RUN echo "deb [arch=i386] http://archive.ubuntu.com/ubuntu/ focal-updates universe" >> /etc/apt/sources.list
RUN echo "deb [arch=i386] http://archive.ubuntu.com/ubuntu/ focal multiverse" >> /etc/apt/sources.list
RUN echo "deb [arch=i386] http://archive.ubuntu.com/ubuntu/ focal-updates multiverse" >> /etc/apt/sources.list
RUN echo "deb [arch=i386] http://archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb [arch=i386] http://security.ubuntu.com/ubuntu/ focal-security main restricted" >> /etc/apt/sources.list
RUN echo "deb [arch=i386] http://security.ubuntu.com/ubuntu/ focal-security universe" >> /etc/apt/sources.list
RUN echo "deb [arch=i386] http://security.ubuntu.com/ubuntu/ focal-security multiverse" >> /etc/apt/sources.list

## add winehq repo and install wine
#RUN apt update; exit 0;
#RUN apt update && apt install -y libstdc++6:i386
#RUN apt-add-repository --remove 'deb https://download.opensuse.org/repositories/Emulators:/Wine:/Debian/Ubuntu_20.04_standard ./'
#RUN wget -nc -O /usr/share/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key
#RUN wget -nc -P /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/ubuntu/dists/focal/winehq-focal.sources
#RUN apt update && apt clean && apt upgrade -y; exit 0;
#RUN apt install -y --install-recommends libncurses5:i386 libncurses6:i386 winehq-stable cabextract xvfb

# Install winehq-stable and with recommends
RUN     wget -nc https://dl.winehq.org/wine-builds/winehq.key \
        && apt-key add winehq.key \
        && echo "deb https://dl.winehq.org/wine-builds/ubuntu/ focal main" >> /etc/apt/sources.list \
#        && wget -O- -q download.opensuse.org/repositories/Emulators:/Wine:/Debian/xUbuntu_20.04/Release.key | apt-key add - \
#        && echo "deb http://download.opensuse.org/repositories/Emulators:/Wine:/Debian/xUbuntu_20.04 ./" | tee /etc/apt/sources.list.d/wine-obs.list \
        && apt update \
		&& apt install -y --install-recommends libncurses5:i386 libncurses6:i386 wine-devel cabextract xvfb

## setup winetricks
RUN wget -q -O /usr/sbin/winetricks https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks \
        && chmod +x /usr/sbin/winetricks

ENV HOME=/home/container
ENV WINEPREFIX=/home/container/.wine
ENV WINEDLLOVERRIDES="mscoree,mshtml="
ENV DISPLAY=:0
ENV DISPLAY_WIDTH=1024
ENV DISPLAY_HEIGHT=768
ENV DISPLAY_DEPTH=16
ENV AUTO_UPDATE=1
ENV XVFB=1


## configure locale
RUN update-locale lang=en_US.UTF-8 && dpkg-reconfigure --frontend noninteractive locales

WORKDIR /home/container
COPY ./entrypoint.sh /entrypoint.sh
CMD ["/bin/bash", "/entrypoint.sh"]