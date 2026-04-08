FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:10.0 AS builder
ARG TARGETARCH

WORKDIR /usr/src/app/

COPY src/cart/src/ ./
COPY pb/ ./pb/

RUN dotnet restore cart.csproj -r linux-musl-$TARGETARCH
RUN dotnet publish cart.csproj -r linux-musl-$TARGETARCH --no-restore -o /cart

FROM mcr.microsoft.com/dotnet/runtime-deps:10.0-alpine

WORKDIR /usr/src/app/
COPY --from=builder /cart/ ./

ENV DOTNET_HOSTBUILDER__RELOADCONFIGONCHANGE=false
ENV CART_PORT=8080

EXPOSE ${CART_PORT}

# FIXED: Entrypoint syntax
ENTRYPOINT ["./cart"]
