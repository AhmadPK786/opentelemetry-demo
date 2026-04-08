# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

############################
# 1️⃣ BUILD STAGE
############################
FROM node:24-slim AS builder

WORKDIR /app

# Install deps first (better caching)
COPY package.json package-lock.json ./
RUN npm ci

# Copy source code
COPY components/ components/
COPY gateways/ gateways/
COPY pages/ pages/
COPY protos/ protos/
COPY providers/ providers/
COPY services/ services/
COPY styles/ styles/
COPY types/ types/

COPY utils/enums/ utils/enums/
COPY utils/telemetry/ utils/telemetry/
COPY utils/imageLoader.js utils/imageLoader.js
COPY utils/Request.ts utils/Request.ts

COPY next.config.js next.config.js
COPY tsconfig.json tsconfig.json

# Build NextJS standalone output
RUN npm run build


############################
# 2️⃣ PRODUCTION DEPS STAGE
############################
FROM node:24-slim AS deps

WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --omit=dev


############################
# 3️⃣ FINAL DISTROLESS IMAGE
############################
FROM gcr.io/distroless/nodejs24-debian13:nonroot

WORKDIR /app

# Next standalone output
COPY --from=builder /app/.next/standalone/ ./
COPY --from=builder /app/.next/static/ .next/static/

# Production node modules
COPY --from=deps /app/node_modules/ node_modules/

# Public assets
COPY public/ public/

# OpenTelemetry instrumentation
COPY utils/telemetry/Instrumentation.js Instrumentation.js

# App port
EXPOSE 3000

# Start server with telemetry preloaded
CMD ["--require=./Instrumentation.js", "server.js"]
