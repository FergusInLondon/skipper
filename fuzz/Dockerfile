FROM amazonlinux:2023@sha256:b0016cbc423cd29bca07d5ff783c432c7f0477dc8d19bdf5828dd560a0b3d0b9

WORKDIR /workspace

ENV PATH="/root/go/bin:$PATH"

COPY . .

RUN dnf install -q -y make clang tar gzip       && \
    update-alternatives --install /usr/bin/cc cc /usr/bin/clang 20 && \
    curl -o go.linux-amd64.tar.gz -LO https://go.dev/dl/go1.22.1.linux-amd64.tar.gz && \
    sha256sum go.linux-amd64.tar.gz | grep -q aab8e15785c997ae20f9c88422ee35d962c4562212bb0f879d052a35c8307c7f && \
    tar -C /root -xzf go.linux-amd64.tar.gz && \
    ls -l /root && \
    go version && \
    go install github.com/mdempsky/go114-fuzz-build@v0.0.0-20200813011514-561a369ae6e1 && \
    go mod init fuzz && \
    go mod tidy && \
    make -s -j $(nproc) && \
    dnf autoremove -q -y && \
    dnf clean all -q && \
    go clean -cache -testcache -modcache -fuzzcache

ENTRYPOINT [ "/usr/bin/make", "fuzz" ]

CMD ["FUZZ_TARGETS=FuzzParseEskip"]
