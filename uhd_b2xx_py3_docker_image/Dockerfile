FROM ubuntu as build

# Everything AFTER the "v" in the GitHub tag
ARG UHD_TAG=4.1.0.5

RUN apt-get update -q && \
    DEBIAN_FRONTEND=noninteractive \
                   apt-get install -qy --no-install-recommends \
                   wget libboost-all-dev libusb-dev libusb-1.0-0 libusb-1.0-0-dev python3-setuptools \
                   python3-pip python3-setuptools python3-numpy \
                   python3-mako python3-requests \
                   cmake build-essential ca-certificates \
                   autoconf automake ccache cpufrequtils \
                   doxygen ethtool g++ git inetutils-tools \
                   libncurses5 libncurses5-dev python3-dev \
                   python3-scipy python3-ruamel.yaml

RUN wget https://github.com/EttusResearch/uhd/archive/refs/tags/v${UHD_TAG}.tar.gz \
    -O uhd.tar.gz && tar -xvzf uhd.tar.gz

RUN mkdir uhd-${UHD_TAG}/host/build
WORKDIR uhd-${UHD_TAG}/host/build
RUN cmake ../
RUN make && make test && make install
RUN python3 /usr/local/lib/uhd/utils/uhd_images_downloader.py

FROM ubuntu

COPY --from=build /usr/local/lib/libuhd.so* /usr/local/lib/
COPY --from=build /usr/local/lib/uhd/ /usr/local/lib/uhd/
COPY --from=build /usr/local/lib/python3/dist-packages/uhd/ /usr/lib/python3/dist-packages/uhd/
COPY --from=build /usr/local/include/uhd.h /usr/local/include/
COPY --from=build /usr/local/include/uhd/  /usr/local/include/uhd/
COPY --from=build /usr/local/bin/uhd_* /usr/local/bin/
COPY --from=build /usr/local/share/uhd/images/usrp_b2* /usr/local/share/uhd/images/

RUN apt-get update -q && \
    DEBIAN_FRONTEND=noninteractive \
                   apt-get install -qy --no-install-recommends \
                   libboost-date-time1.71.0 libboost-filesystem1.71.0 \
                   libboost-python1.71.0 libboost-regex1.71.0 \
                   libboost-serialization1.71.0 libboost-system1.71.0 \
                   libboost-thread1.71.0 libboost-program-options1.71.0 \
		   libboost-chrono1.71.0 \
                   libusb-1.0-0 libpython3.8 python3-setuptools python3-pip \
                   python3-numpy && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

CMD /usr/bin/python3
