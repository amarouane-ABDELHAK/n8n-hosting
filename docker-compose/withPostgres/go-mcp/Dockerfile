# ---------- Build stage ----------
FROM golang:tip-alpine3.23 AS builder

# Install git (needed for go mod download in some cases)
RUN apk add --no-cache git

WORKDIR /app

# Copy go mod files first (better layer caching)
COPY go.mod go.sum ./
RUN go mod download

# Copy the rest of the source code
COPY main.go main.go
COPY help.go help.go

# Build a static binary
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -o app

# ---------- Runtime stage ----------
FROM alpine:3.23.2

WORKDIR /app

# Copy binary from builder
COPY --from=builder /app/app /app/app

# Optional: if your app listens on a port
EXPOSE 8000

# Run the binary
ENTRYPOINT ["/app/app", "-port", "9000", "-host", "0.0.0.0", "server"]
