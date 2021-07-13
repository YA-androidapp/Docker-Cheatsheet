FROM ubuntu
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    iproute2 \
    && apt-get -y clean \
    && rm -rf /var/lib/apt/lists/*
