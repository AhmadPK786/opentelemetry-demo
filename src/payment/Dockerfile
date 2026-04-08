# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

FROM docker.io/library/node:22-slim AS builder

WORKDIR /usr/src/app/

# Context is src/payment, so use direct paths
COPY package.json package-lock.json ./

RUN npm ci --omit=dev

# -----------------------------------------------------------------------------

FROM gcr.io/distroless/nodejs22-debian12:nonroot

WORKDIR /usr/src/app/

COPY --from=builder /usr/src/app/node_modules/ node_modules/

# Copy proto from root context if needed, or skip if not required
# COPY ../../pb/demo.proto demo.proto  # Uncomment if actually needed

COPY charge.js index.js logger.js opentelemetry.js ./

ARG PAYMENT_PORT=8080
ENV PAYMENT_PORT=${PAYMENT_PORT}
EXPOSE ${PAYMENT_PORT}

CMD ["--require=./opentelemetry.js", "index.js"]
