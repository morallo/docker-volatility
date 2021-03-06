FROM blacktop/yara:3.5

MAINTAINER blacktop, https://github.com/blacktop

ENV VOL_VERSION 2.5

# Install Volatility Dependancies
RUN apk-install ca-certificates zlib py-pillow py-crypto py-lxml py-setuptools
RUN apk-install -t .build-deps \
                   openssl-dev \
                   python-dev \
                   build-base \
                   zlib-dev \
                   libc-dev \
                   jpeg-dev \
                   automake \
                   autoconf \
                   py-pip \
                   git \
  && export PIP_NO_CACHE_DIR=off \
  && export PIP_DISABLE_PIP_VERSION_CHECK=on \
  && pip install --upgrade pip wheel \
  && pip install simplejson \
                 construct \
                 openpyxl \
                 haystack \
                 distorm3 \
                 colorama \
                 ipython \
                 pycoin \
                 pytz \
  && cd /tmp \
  && echo "===> Installing Volatility from source..." \
  && git clone --recursive --branch $VOL_VERSION https://github.com/volatilityfoundation/volatility.git \
  && cd volatility \
  && python setup.py build install \
  && rm -rf /tmp/* \
  && apk del --purge .build-deps

VOLUME ["/data"]
VOLUME ["/plugins"]

WORKDIR /data

ENTRYPOINT ["su-exec","nobody","/sbin/tini","--","vol.py"]
CMD ["-h"]
