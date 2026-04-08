FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:10.0 AS builder
ARG TARGETARCH

WORKDIR /usr/src/app/

# Copy everything needed for the build
COPY src/cart/src/ ./
COPY pb/ ./pb/

# 1. Install the dotnet-grpc tool
RUN dotnet tool install -g dotnet-grpc

# 2. Add the tool to PATH
ENV PATH="${PATH}:/root/.dotnet/tools"

# 3. Generate C# code from the proto file(s)
# This command reads ./pb/demo.proto and outputs the generated code
RUN dotnet grpc add-file ./pb/demo.proto --services Default

# 4. Now restore and publish the application
RUN dotnet restore cart.csproj -r linux-musl-$TARGETARCH
RUN dotnet publish cart.csproj -r linux-musl-$TARGETARCH --no-restore -o /cart

# --- Release Stage ---
FROM mcr.microsoft.com/dotnet/runtime-deps:10.0-alpine

WORKDIR /usr/src/app/
COPY --from=builder /cart/ ./

ENV DOTNET_HOSTBUILDER__RELOADCONFIGONCHANGE=false
ENV CART_PORT=8080

EXPOSE ${CART_PORT}

ENTRYPOINT ["./cart"]
