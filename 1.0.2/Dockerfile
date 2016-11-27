FROM debian:jessie

# ensure local openssl is preferred over distribution openssl
ENV PATH /usr/local/ssl/bin:$PATH

ENV GPG_KEY 8657ABB260F056B1E5190839D9C4D26D0E604491
ENV OPENSSL_VERSION 1.0.2j

RUN set -x \
    && buildDeps=' \
				ca-certificates \
        gcc \
        libc6-dev \
        make \
        perl \
        wget \
    ' \
    && apt-get update && apt-get install -y --no-install-recommends $buildDeps \
    && rm -rf /var/lib/apt/lists/* \
    \
    && wget -O openssl.tar.gz "https://www.openssl.org/source/openssl-$OPENSSL_VERSION.tar.gz" \
		&& wget -O openssl.tar.gz.asc "https://www.openssl.org/source/openssl-$OPENSSL_VERSION.tar.gz.asc" \
		&& export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$GPG_KEY" \
    && gpg --batch --verify openssl.tar.gz.asc openssl.tar.gz \
    && rm -r "$GNUPGHOME" openssl.tar.gz.asc \
    && mkdir -p /usr/src/openssl \
    && tar -xzC /usr/src/openssl --strip-components=1 -f openssl.tar.gz \
    && rm openssl.tar.gz \
    \
    && cd /usr/src/openssl \
    && perl ./Configure linux-x86_64 \
        enable-ec_nistp_64_gcc_128 \
				shared \
				-Wl,-rpath=/usr/local/ssl/lib \
    && make depend \
    && make \
    && make test \
    && make install_sw \
		\
# bootstrap with the system CA certificates, a la Homebrew
    && cp /etc/ssl/certs/*.pem /usr/local/ssl/certs/ \
    && c_rehash /usr/local/ssl/certs \
    \
    && apt-get purge -y --auto-remove $buildDeps \
    && rm -rf /usr/src/openssl

CMD ["openssl"]
