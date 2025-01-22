#FROM ocr-docker-remote.artifactory.oci.oraclecorp.com/os/oraclelinux:8-slim-fips as builder
FROM oraclelinux:9-slim-fips as builder
LABEL maintainer="richard.thompson@oracle.com"

RUN microdnf install -y gcc gcc-c++ make curl git glib2-devel ksh bison flex vim openssl

COPY seagull /opt/src/seagull/.

RUN ls -la /opt/src/seagull/*

RUN mkdir -p /opt/src/seagull/seagull/trunk/src/external-lib-src \
    && curl -fSL https://www.sctp.de/download/sctplib-1.0.15.tar.gz -o /opt/src/seagull/seagull/trunk/src/external-lib-src/sctplib-1.0.15.tar.gz \
    && curl -fSL https://www.sctp.de/download/socketapi-2.2.8.tar.gz -o /opt/src/seagull/seagull/trunk/src/external-lib-src/socketapi-2.2.8.tar.gz \
    && curl -fSL https://www.openssl.org/source/openssl-1.0.2e.tar.gz -o /opt/src/seagull/seagull/trunk/src/external-lib-src/openssl-1.0.2e.tar.gz

RUN cd /opt/src/seagull/seagull/trunk/src \
&& ./build-ext-lib.ksh

RUN cd /opt/src/seagull/seagull/trunk/src \
&& ls -la * \
&& pwd \
&& ./build.ksh -target all -exec DEBUG

RUN cat /root/opt/src/seagull/seagull/trunk/src/work-1.8.3/project.mk
RUN echo **********************************************/root/opt/src/seagull/seagull/trunk/src/work-1.8.3/project.mk

RUN ls -la /opt/src/seagull/seagull/trunk/src/*

RUN tar cvf /root/bin.tgz /opt/src/seagull/seagull/trunk/src/bin/* \
&& tar cvf /root/exe-env.tgz /opt/src/seagull/seagull/trunk/src/exe-env/* \
&& tar cvf /root/pkg.tgz /root/exe-env.tgz /root/bin.tgz


#FROM ocr-docker-remote.artifactory.oci.oraclecorp.com/os/oraclelinux:8-slim-fips as distro
FROM oraclelinux:9-slim-fips as distro

RUN microdnf update
RUN microdnf install -y glibc-langpack-en ksh
COPY --from=builder /root/pkg.tgz /root/pkg.tgz
RUN tar xvf /root/pkg.tgz -C /root --strip=1 \
&& tar xvf /root/bin.tgz -C /usr/local/bin --strip=8 \
&& mkdir -p /opt/seagull \
&& tar xvf /root/exe-env.tgz -C /opt/seagull --strip=8 \
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
