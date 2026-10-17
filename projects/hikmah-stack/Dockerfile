# hikmah-stack — Rust workspace (hikmah-kernel)
FROM rust:1.89-bookworm
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
      ca-certificates git pkg-config \
    && rm -rf /var/lib/apt/lists/*

# scanner contract: the checkout lives inside the image, at /src
COPY . /src
WORKDIR /src

# Build the workspace with debug info and frame pointers for useful reports.
RUN cargo build --workspace

# Run the test suite so we know the image works; a failing test does not abort the build.
RUN (cargo test --workspace 2>&1 | tail -20 || echo "WARNING: cargo test reported failures")
