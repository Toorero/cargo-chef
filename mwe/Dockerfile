FROM rust:latest AS chef
RUN cargo install cargo-chef bpf-linker
WORKDIR /mwe


FROM chef AS planner

COPY . .
# not required to install toolchain with additional components just for prepare
# since we need to add it manually anyways
RUN rm -f rust-toolchain.toml

RUN cargo chef prepare --recipe-path recipe.json

FROM chef AS builder

# add required toolchain
# this is required since
# 1) we removed before `chef prepare`
# 2) even if we kept it it wouldn't work since `chef cook` is ignoring it
#    because it is adding the file to late in the cooking
COPY rust-toolchain.toml rust-toolchain.toml

RUN apt update && apt install -y musl-tools musl-dev
RUN update-ca-certificates

# build dependencies
COPY --from=planner "/mwe/recipe.json" recipe.json
RUN cargo chef cook  --release --target x86_64-unknown-linux-musl --recipe-path recipe.json

COPY ./ .
RUN cargo build --target x86_64-unknown-linux-musl --release

FROM ubuntu:latest

WORKDIR /mwe

# Copy our build
COPY --from=builder /mwe/target/x86_64-unknown-linux-musl/release/mwe ./

ENTRYPOINT ["mwe"]

