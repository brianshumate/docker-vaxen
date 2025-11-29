FROM ubuntu:24.04 AS builder

# Set environment variable to avoid locale warnings
ENV LANG=C.UTF-8

# Build dependencies only
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    build-essential \
    ca-certificates \
    git \
    make \
    libsdl2-dev \
    libpng-dev \
    libpcap-dev \
    libvdeplug-dev && \
    rm -rf /var/lib/apt/lists/*

# Build stage
WORKDIR /build
# Clone Open SIMH repository and build the binary
RUN git clone https://github.com/open-simh/simh.git

WORKDIR /build/simh

RUN make -j$(nproc) vax8600 && \
    strip ./BIN/vax8600
RUN chmod +x ./BIN/vax8600

# Final runtime stage
FROM ubuntu:24.04

ENV LANG=C.UTF-8

LABEL maintainer="brian@brianshumate.com" \
    version="1.0" \
    description="Open-SIMH VAX 8600"

# Install runtime dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    libsdl2-2.0-0 \
    libpng16-16 \
    libcap2-bin \
    libpcap0.8 \
    # open-simh needs libpcap-dev for net devices
    libpcap-dev \
    libvdeplug2 && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /vax8600

# Copy compiled binary from builder
COPY --from=builder /build/simh/BIN/vax8600 /usr/local/bin/

# Copy runtime files as needed
# COPY --chown=nobody:nogroup vax8600.ini /vax8600/vax8600.ini

# Set necessary capability on binary for emulation networking
RUN setcap 'cap_net_raw+ep cap_net_admin=+ep' /usr/local/bin/vax8600

# Run as non-root user
USER nobody

CMD ["vax8600"]
