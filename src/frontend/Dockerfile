# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

FROM docker.io/library/node:24-slim AS builder

WORKDIR /app

# Context "src/frontend" hai, toh seedha file names likho
COPY package.json package-lock.json ./

RUN npm ci

# Sab kuch current directory (src/frontend) se copy ho raha hai
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

RUN npm run build

# -----------------------------------------------------------------------------

FROM docker.io/library/node:24-slim AS deps

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm ci --omit=dev

# -----------------------------------------------------------------------------

FROM gcr.io/distroless/nodejs24-debian13:nonroot

WORKDIR /app

COPY --from=builder /app/.next/standalone/ ./
COPY --from=builder /app/.next/static/ .next/static/

COPY --from=deps /app/node_modules/ node_modules/

COPY public/ public/

# Instrumentation file bhi builder stage se copy karna better hoga
COPY --from=builder /app/utils/telemetry/Instrumentation.js ./Instrumentation.js

ARG FRONTEND_PORT=3000
ENV FRONTEND_PORT=${FRONTEND_PORT}
ENV PORT=${FRONTEND_PORT}
ENV HOSTNAME=0.0.0.0

EXPOSE ${FRONTEND_PORT}

CMD ["--require=./Instrumentation.js", "server.js"]# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

FROM docker.io/library/node:24-slim AS builder

WORKDIR /app

COPY ./src/frontend/package.json package.json
COPY ./src/frontend/package-lock.json package-lock.json

RUN npm ci

COPY ./src/frontend/components/ components/
COPY ./src/frontend/gateways/ gateways/
COPY ./src/frontend/pages/ pages/
COPY ./src/frontend/protos/ protos/
COPY ./src/frontend/providers/ providers/
COPY ./src/frontend/services/ services/
COPY ./src/frontend/styles/ styles/
COPY ./src/frontend/types/ types/

COPY ./src/frontend/utils/enums/ utils/enums/
COPY ./src/frontend/utils/telemetry/ utils/telemetry/
COPY ./src/frontend/utils/imageLoader.js utils/imageLoader.js
COPY ./src/frontend/utils/Request.ts utils/Request.ts

COPY ./src/frontend/next.config.js next.config.js
COPY ./src/frontend/tsconfig.json tsconfig.json

RUN npm run build

# -----------------------------------------------------------------------------

FROM docker.io/library/node:24-slim AS deps

WORKDIR /app

COPY ./src/frontend/package.json package.json
COPY ./src/frontend/package-lock.json package-lock.json

RUN npm ci --omit=dev

# -----------------------------------------------------------------------------

FROM gcr.io/distroless/nodejs24-debian13:nonroot

WORKDIR /app

COPY --from=builder /app/.next/standalone/ ./
COPY --from=builder /app/.next/static/ .next/static/

COPY --from=deps /app/node_modules/ node_modules/

COPY ./src/frontend/public/ public/

COPY ./src/frontend/utils/telemetry/Instrumentation.js Instrumentation.js

EXPOSE ${FRONTEND_PORT}

CMD ["--require=./Instrumentation.js", "server.js"]
