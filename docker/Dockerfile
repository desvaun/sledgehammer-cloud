FROM rockylinux:9

LABEL org.opencontainers.image.title="SledgeHammer Studio PD Worker"
LABEL org.opencontainers.image.description="Containerized SledgeHammer worker environment for cloud physical design evaluation"

# Pin the exact SledgeHammer revision used for the evaluation.
ARG SLEDGEHAMMER_COMMIT=7d3565b46b94b18cff8db021a7da360876a36156

# Install base development and runtime dependencies.
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
        libnsl \
        libpq \
        openldap \
        cyrus-sasl-devel \
        openssl-devel && \
    dnf clean all

WORKDIR /opt

# Clone SledgeHammer and check out the pinned revision.
RUN git clone https://github.com/lawrencejrhee/hammer.git sledgehammer && \
    cd sledgehammer && \
    git checkout "${SLEDGEHAMMER_COMMIT}"

WORKDIR /opt/sledgehammer

# Standalone Step 2 environment: no external technology plugins yet.
ENV SLEDGE_NO_PLUGINS=1

# Run the repository's own environment setup.
# Answer "n" to the Postgres secrets prompt; database integration comes later.
RUN printf 'n\n' | ./scripts/uv_setup.sh

# Make the SledgeHammer virtual environment available by default.
ENV PATH="/opt/sledgehammer/.venv/bin:/root/.local/bin:${PATH}"

CMD ["/bin/bash"]
