FROM --platform=linux/amd64 python:3.8-slim-buster

ARG DEBIAN_FRONTEND=noninteractive

COPY ./ ./TenSEAL
WORKDIR /TenSEAL

RUN .github/workflows/scripts/install_req_docker.sh

ENV CC=clang \
    CXX=clang++

#RUN pip3 install .
RUN python3 setup.py install

# Install required packages
RUN apk add --update --virtual=.build-dependencies alpine-sdk nodejs ca-certificates musl-dev gcc python-dev make cmake g++ gfortran libpng-dev freetype-dev libxml2-dev libxslt-dev
RUN apk add --update git

# Install Jupyter
RUN pip install jupyter
RUN pip install ipywidgets
RUN jupyter nbextension enable --py widgetsnbextension

# Install JupyterLab
RUN pip install jupyterlab && jupyter serverextension enable --py jupyterlab

# Additional packages for compatability (glibc)
RUN wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://raw.githubusercontent.com/sgerrand/alpine-pkg-glibc/master/sgerrand.rsa.pub && \
  wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.23-r3/glibc-2.23-r3.apk && \
  wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.23-r3/glibc-i18n-2.23-r3.apk && \
  wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.23-r3/glibc-bin-2.23-r3.apk && \
  apk add --no-cache glibc-2.23-r3.apk glibc-bin-2.23-r3.apk glibc-i18n-2.23-r3.apk && \
  rm "/etc/apk/keys/sgerrand.rsa.pub" && \
  /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 C.UTF-8 || true && \
  echo "export LANG=C.UTF-8" > /etc/profile.d/locale.sh && \
  ln -s /usr/include/locale.h /usr/include/xlocale.h

# Optional Clean-up
#  RUN apk del glibc-i18n && \
#  apk del .build-dependencies && \
#  rm glibc-2.23-r3.apk glibc-bin-2.23-r3.apk glibc-i18n-2.23-r3.apk && \
#  rm -rf /var/cache/apk/*

ENV LANG=C.UTF-8

# Install Python Packages & Requirements (Done near end to avoid invalidating cache)
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

# Expose Jupyter port & cmd
EXPOSE 8888
RUN mkdir -p /opt/app/data
CMD jupyter lab --ip=* --port=8888 --no-browser --notebook-dir=/opt/app/data --allow-root

ENTRYPOINT [ "/bin/bash" ]


#FROM python:alpine
