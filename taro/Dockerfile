FROM golang:1.19.2-alpine as builder

# Force Go to use the cgo based DNS resolver. This is required to ensure DNS
# queries required to connect to linked containers succeed.
ENV GODEBUG netdns=cgo

# Pass a tag, branch or a commit using build-arg.  This allows a docker
# image to be built from a specified Git state.  The default image
# will use the Git tip of master by default.
ARG checkout="main"
ARG git_url="https://github.com/lightninglabs/taro"

# Install dependencies and build the binaries.
RUN apk add --no-cache --update alpine-sdk \
    git \
    make \
    gcc \
&&  git clone $git_url /go/src/github.com/lightninglabs/taro \
&&  cd /go/src/github.com/lightninglabs/taro \
&&  git checkout $checkout \
&&  make install

# Start a new, final image.
FROM alpine as final

# Define a root volume for data persistence.
VOLUME /root/.taro

# Add utilities for quality of life and SSL-related reasons. We also require
# curl and gpg for the signature verification script.
RUN apk --no-cache add \
    bash \
    jq \
    ca-certificates \
    gnupg \
    curl

# Copy the binaries from the builder image and configuration. \
COPY --from=builder /go/bin/tarod /bin/
COPY --from=builder /go/bin/tarocli /bin/
COPY taro.conf /root/.taro

# Expose ports
EXPOSE 10029 8089

# Specify the start command and entrypoint as the lnd daemon.
ENTRYPOINT ["tarod"]