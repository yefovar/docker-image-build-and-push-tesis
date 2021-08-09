FROM ubuntu:bionic
USER root
ENV TIMEZONE America/Mexico_City
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
ENV DEBIAN_FRONTEND noninteractive 
ENV DEB_BUILD_DEPS="sudo nano less git wget curl python3-dev python3-pip python3-setuptools"
ENV DEB_PACKAGES=""
ENV PIP_PACKAGES_COMMON="selenium==3.141.0"
ENV PIP_PACKAGES_KALE="kubeflow-kale==0.6.1"

RUN apt-get update && export $DEBIAN_FRONTEND && \
    echo $TIMEZONE > /etc/timezone && apt-get install -y tzdata

RUN apt-get update && apt-get install -y $DEB_BUILD_DEPS $DEB_PACKAGES && pip3 install --upgrade pip  

RUN curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash - && apt-get install -y nodejs

RUN pip3 install jupyter "jupyterlab>=2.0.0,<3.0.0" --upgrade

#next password is dummy

RUN jupyter notebook --generate-config && \
    sed -i "s/#c.NotebookApp.password = .*/c.NotebookApp.password = u'sha1:ad72cde9917c:a428547837a67cc003a46a942d2c35a55494c74f'/" ~/.jupyter/jupyter_notebook_config.py && \
    sed -i "s/#c.NotebookApp.certfile = .*/c.NotebookApp.certfile = u'\/shared_volume\/certs\/cert.pem'/" ~/.jupyter/jupyter_notebook_config.py && \
    sed -i "s/#c.NotebookApp.keyfile = .*/c.NotebookApp.keyfile = u'\/shared_volume\/certs\/privkey.key'/" ~/.jupyter/jupyter_notebook_config.py

RUN pip install $PIP_PACKAGES_COMMON --upgrade
RUN pip install $PIP_PACKAGES_KALE --upgrade
RUN jupyter labextension install kubeflow-kale-labextension@0.6.1

RUN git clone https://github.com/yefovar/docker-image-build-and-push-tesis.git
RUN pip install -r docker-image-build-and-push-tesis/requirements.txt

VOLUME ["/shared_volume"]

WORKDIR /

ENV NB_PREFIX myurl

ENTRYPOINT ["/usr/local/bin/jupyter", "lab", "--ip=0.0.0.0", "--no-browser", "--allow-root", "--LabApp.allow_origin='*'", "--LabApp.base_url=myurl"]
