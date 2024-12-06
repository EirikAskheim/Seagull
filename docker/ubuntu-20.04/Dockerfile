# Use an Ubuntu base image
FROM ubuntu:20.04

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    curl \
    git \
    libglib2.0-dev \
    ksh \
    bison \
    flex \
    vim \
    tmux \
    tcpdump \
    libpcap-dev

# Clone the Seagull source code
RUN mkdir -p /opt/src \
    && cd /opt/src \
    && git clone https://github.com/EirikAskheim/Seagull.git seagull

# Download and unpack SCTP, Socket API, and OpenSSL external libraries
RUN mkdir -p /opt/src/seagull/seagull/trunk/src/external-lib-src \
    && curl -fSL https://www.sctp.de/download/sctplib-1.0.15.tar.gz -o /opt/src/seagull/seagull/trunk/src/external-lib-src/sctplib-1.0.15.tar.gz \
    && curl -fSL https://www.sctp.de/download/socketapi-2.2.8.tar.gz -o /opt/src/seagull/seagull/trunk/src/external-lib-src/socketapi-2.2.8.tar.gz \
    && curl -fSL https://www.openssl.org/source/openssl-1.0.2e.tar.gz -o /opt/src/seagull/seagull/trunk/src/external-lib-src/openssl-1.0.2e.tar.gz

# Unpack and build the external libraries
WORKDIR /opt/src/seagull/seagull/trunk/src
RUN ksh build-ext-lib.ksh \
    && ksh build.ksh -target all

# Copy the binaries to a directory in PATH
RUN mkdir -p /usr/local/bin \
    && cp /opt/src/seagull/seagull/trunk/src/bin/* /usr/local/bin/

# Clean up unnecessary files to reduce image size
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Expose ports for Diameter
EXPOSE 3868/tcp
EXPOSE 5036/udp

# Set the default command
CMD ["/bin/bash"]
