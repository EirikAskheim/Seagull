FROM oraclelinux:9-slim-fips AS builder
LABEL org.opencontainers.image.source="https://github.com/EirikAskheim/Seagull"

RUN microdnf install -y dnf \
    && dnf --enablerepo=ol9_codeready_builder install -y  gcc gcc-c++ make curl git glib2-devel ksh bison flex vim openssl flex-devel libtool

COPY seagull /opt/src/seagull/seagull

RUN    mkdir -p /opt/src/seagull/seagull/trunk/src/external-lib-src \
    && curl -fSL --create-dirs -o /opt/src/seagull/seagull/trunk/src/external-lib-src/sctplib-1.0.31.tar.gz  https://www.nntb.no/~dreibh/sctplib/download/sctplib-1.0.31.tar.gz \
    && curl -fSL --create-dirs -o /opt/src/seagull/seagull/trunk/src/external-lib-src/socketapi-2.2.24.tar.gz https://www.nntb.no/~dreibh/sctplib/download/socketapi-2.2.24.tar.gz

RUN    cd /opt/src/seagull/seagull/trunk/src/external-lib-src \
    && gunzip sctplib-1.0.31.tar.gz \
    && tar -xvf sctplib-1.0.31.tar \
    && cd sctplib-1.0.31 \
    && ./configure \
    && make \
    && make install \
    && cd /opt/src/seagull/seagull/trunk/src/external-lib-src \
    && gunzip socketapi-2.2.24.tar.gz \
    && tar -xvf socketapi-2.2.24.tar \
    && cd socketapi-2.2.24 \
    && ./configure \
    && make \
    && make install

RUN cd /opt/src/seagull/seagull/trunk/src \
&& ./build.ksh -target all

RUN tar cvf /root/bin.tgz /opt/src/seagull/seagull/trunk/src/bin/* \
&& tar cvf /root/exe-env.tgz /opt/src/seagull/seagull/trunk/src/exe-env/* \
&& tar cvf /root/pkg.tgz /root/exe-env.tgz /root/bin.tgz

FROM oraclelinux:9-slim-fips AS distro

RUN microdnf update
RUN microdnf install -y glibc-langpack-en ksh
COPY --from=builder /root/pkg.tgz /root/pkg.tgz
RUN tar xvf /root/pkg.tgz -C /root --strip=1 \
&& tar xvf /root/bin.tgz -C /usr/local/bin --strip=7 \
&& mkdir -p /opt/seagull \
&& tar xvf /root/exe-env.tgz -C /opt/seagull --strip=7 \
&& mkdir -p /opt/seagull/diameter-env/logs \
&& mkdir -p /opt/seagull/h248-env/logs \
&& mkdir -p /opt/seagull/http-env/logs \
&& mkdir -p /opt/seagull/msrp-env/logs \
&& mkdir -p /opt/seagull/octcap-env/logs \
&& mkdir -p /opt/seagull/radius-env/logs \
&& mkdir -p /opt/seagull/sip-env/logs \
&& mkdir -p /opt/seagull/synchro-env/logs \
&& mkdir -p /opt/seagull/xcap-env/logs
RUN rm -f /root/*.tgz
