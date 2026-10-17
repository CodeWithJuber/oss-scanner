# wisdomlens — agent skills repo (markdown + node validation scripts)
FROM node:22-bookworm-slim
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
      ca-certificates git \
    && rm -rf /var/lib/apt/lists/*

# scanner contract: the checkout lives inside the image, at /src
COPY . /src
WORKDIR /src

# Run the repo's own validation suite so we know the image works.
# A failing check does not abort the build.
RUN (node scripts/validate.mjs 2>&1 | tail -20 || echo "WARNING: validate.mjs reported failures")
RUN (node scripts/trigger-test.mjs 2>&1 | tail -10 || echo "WARNING: trigger-test reported failures")
