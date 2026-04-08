FROM docker.io/library/node:22-slim AS builder

WORKDIR /usr/src/app/

COPY package.json package-lock.json ./
RUN npm ci --omit=dev

FROM gcr.io/distroless/nodejs22-debian12:nonroot

WORKDIR /usr/src/app/

COPY --from=builder /usr/src/app/node_modules/ node_modules/
COPY charge.js index.js logger.js opentelemetry.js ./

ARG PAYMENT_PORT=8080
ENV PAYMENT_PORT=${PAYMENT_PORT}
EXPOSE ${PAYMENT_PORT}

CMD ["index.js"]
