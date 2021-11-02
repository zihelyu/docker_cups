#airprint 梓晨网络 www.dawnwl.com

FROM alpine:latest

WORKDIR /usr/src/cups

# 更换国内源

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk update

# 安装依赖
RUN apk add tzdata \
  curl \
  make \
  gcc \
  musl-dev \
  groff \
  vim \
  avahi \
  cups \
  cups-libs \
  cups-filters

RUN echo "root:password" | chpasswd

# set timezome

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

COPY foo2zjs /usr/src/cups/foo2zjs

# install devices

RUN cd foo2zjs \
    && make \
    && make install \
    && rm -rf foo2zjs

# setting dbus
RUN if [ ! -d '/var/run/dbus' ];then \
    mkdir -p /var/run/dbus \
    && chown messagebus /var/run/dbus \
    && chgrp messagebus /var/run/dbus \
    ;fi


# enable remote-any
RUN /usr/sbin/cupsd \
    && cupsctl --remote-admin --remote-any --share-printers \
    && kill -9 `cat /var/run/cups/cupsd.pid` \
    && rm -f /var/run/cups/cups.sock /var/run/cups/cupsd.pid

ENTRYPOINT ["/bin/sh", "-c", "rm -f /var/run/avahi-daemon/* /var/run/cups/cups.sock /var/run/cups/cupsd.pid /var/run/dbus.pid /var/run/dbus/* && cat /usr/share/foo2zjs/firmware/sihp1020.dl > /dev/usb/lp0 && /usr/bin/dbus-daemon --system && /usr/sbin/avahi-daemon -D && /usr/sbin/cupsd -f"]