FROM debian:12-slim AS build

ARG SPADES_VERSION=4.3.0
ARG SPADES_SHA256=09671ca39f9c6d2479d9fc168100bfd089b4a24002d51b815386d2b24d424456
ARG DEBIAN_FRONTEND=noninteractive

RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
      ca-certificates \
      curl \
      build-essential \
      cmake \
      python3 \
      zlib1g-dev \
      libbz2-dev; \
    rm -rf /var/lib/apt/lists/*

WORKDIR /tmp/spades-build

RUN set -eux; \
    curl -L "https://github.com/ablab/spades/releases/download/v${SPADES_VERSION}/SPAdes-${SPADES_VERSION}.tar.gz" -o spades.tar.gz; \
    echo "${SPADES_SHA256}  spades.tar.gz" | sha256sum -c -; \
    tar -xzf spades.tar.gz --strip-components=1; \
    PREFIX=/opt/spades ./spades_compile.sh -j 2 '-DSPADES_ENABLE_PROJECTS=spades;spades_tools;binspreader;pathracer;spaligner;splitter'; \
    test "$(cat /opt/spades/share/spades/VERSION)" = "${SPADES_VERSION}"; \
    /opt/spades/bin/spades.py --version 2>&1 | grep -F "SPAdes genome assembler v${SPADES_VERSION}" >/dev/null

FROM debian:12-slim

ARG DEBIAN_FRONTEND=noninteractive

RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
      ca-certificates \
      bash \
      bzip2 \
      coreutils \
      gzip \
      libbz2-1.0 \
      libgcc-s1 \
      libgomp1 \
      libstdc++6 \
      python3 \
      sed \
      xz-utils \
      zlib1g; \
    rm -rf /var/lib/apt/lists/*

COPY --from=build /opt/spades /opt/spades

ENV PATH="/opt/spades/bin:${PATH}"
ENV TAFFISH_ENV=TAFFISH
ENV TAFFISH_NAME=spades

RUN set -eux; \
    spades.py --version 2>&1 | grep -F "SPAdes genome assembler v4.3.0" >/dev/null; \
    spades.py --help 2>&1 | grep -F "SPAdes genome assembler" >/dev/null; \
    spades.py --help 2>&1 | grep -F -- "--isolate" >/dev/null; \
    spades.py --help 2>&1 | grep -F -- "--meta" >/dev/null; \
    spades.py --help 2>&1 | grep -F -- "--frugal" >/dev/null; \
    metaspades.py --help 2>&1 | grep -i "meta" >/dev/null; \
    plasmidspades.py --help 2>&1 | grep -i "plasmid" >/dev/null; \
    rnaspades.py --help 2>&1 | grep -i "rna" >/dev/null; \
    splitter --help 2>&1 | grep -F "graph (in binary or GFA)" >/dev/null; \
    for x in spades-core spades-hammer spades-corrector-core spades-ionhammer spades-kmercount spades-kmer-estimating spades-read-filter spades-gbuilder spades-gmapper spades-gsimplifier spades-gfa-split binspreader pathracer pathracer-seq-fs spaligner splitter; do \
      command -v "$x" >/dev/null; \
      ! ldd "$(command -v "$x")" 2>&1 | grep -F "not found" >/dev/null; \
    done; \
    mkdir -p /tmp/taf-spades-build-check; \
    cd /tmp/taf-spades-build-check; \
    spades.py --test -t 1 > run.log 2>&1; \
    test -s spades_test/contigs.fasta; \
    test -s spades_test/scaffolds.fasta; \
    grep -F "TEST PASSED CORRECTLY" run.log >/dev/null; \
    rm -rf /tmp/taf-spades-build-check

WORKDIR /root
