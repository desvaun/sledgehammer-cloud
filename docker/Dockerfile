FROM rockylinux:9

LABEL org.opencontainers.image.title="SledgeHammer Studio PD Worker"
LABEL org.opencontainers.image.description="Containerized SledgeHammer worker environment for cloud physical design evaluation"

# Pin the exact SledgeHammer revision used for the evaluation.
ARG SLEDGEHAMMER_COMMIT=7d3565b46b94b18cff8db021a7da360876a36156

# Install base development dependencies plus the Rocky Linux runtime
# dependencies required by Cadence Genus and Innovus.
#
# Cadence itself is NOT included in this image. The commercial tool
# installation is mounted read-only from the host at runtime.
RUN dnf update -y && \
    dnf install -y \
        git \
        curl-minimal \
        wget \
        gcc \
        gcc-c++ \
        make \
        dnf-plugins-core \
        rpm \
        cpio \
        libpq \
        openldap \
        cyrus-sasl-devel \
        openssl-devel \
        ksh \
        pinentry \
        libpng \
        motif \
        mesa-libGLU \
        libXScrnSaver \
        gd \
        pcre2-utf16 \
        xcb-util-wm \
        xcb-util-image \
        xcb-util-keysyms \
        xcb-util-renderutil \
        libxkbcommon-x11 \
        libxkbcommon \
        nss \
        libXcomposite \
        libXdamage \
        libXtst \
        pciutils-libs \
        alsa-lib \
        libssh \
        libX11-xcb \
        libXft \
        libnsl \
        libglvnd-glx \
        libSM \
        libICE \
        libXp \
        libXmu \
        libXt \
        tcsh \
        xterm \
        numactl-libs \
        file \
        procps-ng && \
    dnf clean all

WORKDIR /opt

# Clone SledgeHammer and check out the pinned revision.
RUN git clone https://github.com/lawrencejrhee/hammer.git sledgehammer && \
    cd sledgehammer && \
    git checkout "${SLEDGEHAMMER_COMMIT}"

WORKDIR /opt/sledgehammer

# Standalone Step 2 environment: no external technology plugins yet.
ENV SLEDGE_NO_PLUGINS=1

# Genus expects a valid user identity and writable temporary directory.
ENV USER=root \
    LOGNAME=root \
    TMPDIR=/tmp/genus-root

RUN mkdir -p "${TMPDIR}" && chmod 1777 "${TMPDIR}"

# Run the repository's own environment setup.
# Answer "n" to the Postgres secrets prompt; database integration comes later.
RUN printf 'n\n' | ./scripts/uv_setup.sh

# Sky130 core-level cloud flows do not use pad timing libraries.
# The upstream plugin adds the full sky130_fd_io Liberty collection, several
# files from which are not accepted by Genus 25.1. Apply the validated
# core-only library selection used by the successful GCD and PASS cloud flows.
COPY patches/sky130/sky130-core-libraries.patch /tmp/sky130-core-libraries.patch
RUN git apply --check /tmp/sky130-core-libraries.patch && \
    git apply /tmp/sky130-core-libraries.patch && \
    rm /tmp/sky130-core-libraries.patch

COPY patches/sky130/sram22-full-cache.patch /tmp/sram22-full-cache.patch
RUN git apply --check /tmp/sram22-full-cache.patch && \
    git apply /tmp/sram22-full-cache.patch && \
    rm /tmp/sram22-full-cache.patch

# Make the SledgeHammer virtual environment available by default.
ENV PATH="/opt/sledgehammer/.venv/bin:/root/.local/bin:${PATH}"

CMD ["/bin/bash"]
