FROM blacktop/yara:3.5

MAINTAINER blacktop, https://github.com/blacktop

ENV VOL_VERSION 2.6

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
                 construct==2.5.5-reupload \
                 openpyxl \
                 haystack \
                 distorm3 \
                 colorama \
                 ipython \
                 pycoin \
                 pytz \
                 pysocks \
                 requests \
                 dpapick \

  && echo "===> Installing Volatility from source..." \
  && cd /tmp \
  && git clone --recursive --branch $VOL_VERSION https://github.com/volatilityfoundation/volatility.git \
  && cd volatility \
  && python setup.py build install \
  && echo "===> Installing Community Plugins..." \
  && mkdir /plugins \
  && cd /plugins \
  && git clone https://github.com/volatilityfoundation/community.git \
  && rm -rf community/AlexanderTarasenko/windbg.py \
  && rm -rf community/MarcinUlikowski/bitlocker.py \
  && mv /tmp/volatility/contrib/plugins contrib \
  && echo " * Installing ioc_writer..." \
  && cd /tmp \
  && git clone --recursive --branch v0.2.2 https://github.com/mandiant/ioc_writer.git \
  && cd ioc_writer \
  && python setup.py install \
  && rm -rf /tmp/* \
  && apk del --purge .build-deps

# Temporary hotfixes for upstream volatility/community import errors
# https://github.com/volatilityfoundation/community/pull/19
RUN cd /plugins/community/YingLi \
  && touch __init__.py \
  && cd /plugins/community/StanislasLejay/linux \
  && touch __init__.py \
  && cd /plugins/community/DatQuoc \
  && touch __init__.py \
  && cd /plugins/community/DimaPshoul \
  && sed -i 's/import volatility.plugins.malware.callstacks as/import/' malthfind.py

VOLUME ["/data"]
VOLUME ["/plugins"]

WORKDIR /data

ENTRYPOINT ["su-exec","nobody","/sbin/tini","--","vol.py","--plugins=/plugins"]
CMD ["-h"]
