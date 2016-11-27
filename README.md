# docker-openssl

[![Build Status](https://travis-ci.org/JayH5/docker-openssl.svg?branch=master)](https://travis-ci.org/JayH5/docker-openssl)

Dockerfiles for OpenSSL built from source.

## Build details
OpenSSL is built mostly with the standard build configuration. The target platform is `linux-x86_64` and the `enable-ec_nistp_64_gcc_128` option is set. Otherwise, everything is the default. That means no SSL v2.0 or v3.0 and no zlib compression support.

### `--prefix` and `--openssldir`
The default `--prefix` and `--openssldir` options are used. This means different things on OpenSSL 1.0.2 and 1.1.0.

#### OpenSSL 1.0.2
OpenSSL is installed to `/usr/local/ssl` (`--prefix`) and the config files (`--openssldir`) are stored there too. Importantly, this means that OpenSSL 1.0.2 is not in the default library load path and should not conflict with the OpenSSL provided by the distribution. You will need to set `LD_LIBRARY_PATH=/usr/local/ssl` when running any software linked against this OpenSSL.

#### OpenSSL 1.1.0
In this version of OpenSSL, the defaults and behaviour for `--prefix` and `--openssldir` changed. The default prefix is `/usr/local` and the config directory is in `<prefix>/ssl`. This means that this OpenSSL *will* be included in the default library load path. However, this shouldn't conflict with the distribution-provided OpenSSL as the platform used (Debian Jessie) still uses OpenSSL 1.0.1 and the library name has changed in OpenSSL 1.1.0 (`libssl.so.1.0.0` vs `libssl.so.1.1`).

### CA certificates
The system CA certificates are copied into the certificate store directory for the built OpenSSL and rehashed. This is similar to what [Homebrew](https://github.com/Homebrew/homebrew-core/blob/master/Formula/openssl%401.1.rb) does.

To update the CA certificates, it's probably best to reinstall the `ca-certificates` package, update the system certificates, and then purge and recopy the system certificates to the local certificate store directory (`/usr/local/ssl/certs`).
