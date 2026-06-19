ARG VAULT_VERSION=1.6.2
ARG TERRAFORM_VERSION=0.14.3
ARG BUILD_FROM
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/ash", "-eo", "pipefail", "-c"]

###
# Create a vault user and group first so the IDs get set the same way,
# even as the rest of this may change over time.
RUN addgroup vault && \
    adduser -S -G vault vault

###
# Set up certificates, our base tools, and Vault.
ARG VAULT_VERSION
ARG TERRAFORM_VERSION
# hadolint ignore=DL3003,DL3018,DL3047
RUN set -eux; \
    apk add --no-cache ca-certificates openssl libcap su-exec dumb-init tzdata nginx unzip && \
    apkArch="$(apk --print-arch)"; \
    case "$apkArch" in \
    aarch64) ARCH='arm64' ;; \
    x86_64) ARCH='amd64' ;; \
    *) echo >&2 "error: unsupported architecture: $apkArch"; exit 1 ;; \
    esac && \
    mkdir -p /tmp/build && \
    cd /tmp/build && \
    wget -q "https://releases.hashicorp.com/vault/${VAULT_VERSION}/vault_${VAULT_VERSION}_linux_${ARCH}.zip" && \
    wget -q "https://releases.hashicorp.com/vault/${VAULT_VERSION}/vault_${VAULT_VERSION}_SHA256SUMS" && \
    grep "vault_${VAULT_VERSION}_linux_${ARCH}.zip" "vault_${VAULT_VERSION}_SHA256SUMS" | sha256sum -c && \
    unzip -d /bin "vault_${VAULT_VERSION}_linux_${ARCH}.zip" && \
    wget -q "https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_${ARCH}.zip" && \
    wget -q "https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_SHA256SUMS" && \
    grep "terraform_${TERRAFORM_VERSION}_linux_${ARCH}.zip" "terraform_${TERRAFORM_VERSION}_SHA256SUMS" | sha256sum -c && \
    unzip -d /bin "terraform_${TERRAFORM_VERSION}_linux_${ARCH}.zip" && \
    cd /tmp && \
    rm -rf /tmp/build && \
    mkdir -p /vault/logs /vault/file /vault/config /vault/raft /vault/terraform && \
    chown -R vault:vault /vault

COPY /terraform/* /vault/terraform/

###
# 8200/tcp is the primary interface that applications use to interact with
# Vault. 8021 for the cluster
EXPOSE 8200 8201

###
# vault config template
COPY vault.hcl.template /vault/config/

# Copy root filesystem
COPY rootfs /

ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Kalw <kalw@indolore.net>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Add-ons" \
    org.opencontainers.image.authors="${GITHUB_REPOSITORY_OWNER}" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
