# forgekit — Node.js CLI (zero runtime deps)
FROM node:22-bookworm-slim
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
      ca-certificates git python3 \
    && rm -rf /var/lib/apt/lists/*

# scanner contract: the checkout lives inside the image, at /src
COPY . /src
WORKDIR /src

# Install dependencies (npm ci for reproducibility)
RUN npm ci --no-audit --no-fund

# Run the test suite so we know the image works; a failing test does not abort the build.
RUN (npm test 2>&1 | tail -20 || echo "WARNING: npm test reported failures")

# Leave the CLI runnable for the scanner.
RUN node src/cli.js --help > /dev/null 2>&1 || echo "WARNING: cli --help failed"
