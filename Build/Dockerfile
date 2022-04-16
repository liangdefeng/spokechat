FROM centos:7

LABEL maintainer="Peter Liang (defeng.liang.cn@gmail.com)" \
      product="Ejabberd development environment"

ENV ERLANG_VERSION 21.3.5

RUN yum -y update \
&& yum -y install git wget curl automake make openssl-devel \
&& yum -y install automake autoconf ncurses-devel libyaml-devel \
&& yum -y install expat-devel gcc gcc-c++ java xsltproc \
&& git clone https://github.com/kerl/kerl.git /tmp/kerl/ \
&& /tmp/kerl/kerl build $ERLANG_VERSION $ERLANG_VERSION \
&& /tmp/kerl/kerl install $ERLANG_VERSION /opt/erlang/$ERLANG_VERSION/

CMD /bin/bash