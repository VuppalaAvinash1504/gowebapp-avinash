FROM golang:1.22-alpine AS builder

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o server .

FROM alpine:3.20

RUN apk add --no-cache ca-certificates bash

WORKDIR /app

COPY --from=builder /app/server .
COPY --from=builder /app/wait-for-it.sh .
RUN chmod +x wait-for-it.sh

EXPOSE 8081

ENTRYPOINT ["./wait-for-it.sh", "postgres:5432", "--", "./server"]