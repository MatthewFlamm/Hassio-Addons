ARG BUILD_FROM

FROM $BUILD_FROM AS build-base

RUN apk add --no-cache \
      python3 \
      py3-gst \
      py3-pip \
      mopidy \
      && ln -s /usr/lib/libpython3.10.so.1.0 /usr/lib/libpython3.10.so


FROM build-base AS mopidy-iris

RUN pip3 install --no-cache-dir --user Mopidy-Iris==3.64.1 &&\
      ls -al /root/.local/lib/python3.10/site-packages


FROM build-base AS mopidy-mpd

RUN pip3 install --no-cache-dir --user Mopidy-MPD==3.3.0 &&\
      ls -al /root/.local/lib/python3.10/site-packages


FROM build-base AS mopidy-radionet

RUN pip3 install --no-cache-dir --user Mopidy-RadioNet==0.2.2 &&\
      ls -al /root/.local/lib/python3.10/site-packages


FROM $BUILD_FROM AS run

RUN apk add --no-cache \
      python3 \
      py3-gst \
      mopidy \
      ca-certificates \
      sudo \
      cifs-utils \
      keyutils \
      && ln -s /usr/lib/libpython3.10.so.1.0 /usr/lib/libpython3.10.so

COPY --from=mopidy-iris /root/.local/lib/python3.10/site-packages /usr/lib/python3.10/site-packages/
COPY --from=mopidy-mpd /root/.local/lib/python3.10/site-packages /usr/lib/python3.10/site-packages/
COPY --from=mopidy-radionet /root/.local/lib/python3.10/site-packages /usr/lib/python3.10/site-packages/

RUN echo "root ALL=NOPASSWD: /usr/lib/python3.10/site-packages/mopidy_iris/system.sh" >> /etc/sudoers

RUN mkdir -p /scripts
COPY scripts/mount_smb.sh /scripts/mount_smb.sh
RUN chmod a+x /scripts/mount_smb.sh

COPY root /

ENTRYPOINT [ "/init" ]

