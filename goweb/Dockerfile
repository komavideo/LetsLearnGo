#build stage
FROM golang:1.20.8-alpine AS builder
RUN apk add --no-cache git
WORKDIR /go/src/app
ENV GIN_MODE release
COPY . .
RUN go get -d -v ./...
RUN go build -o /go/bin/app -v ./...

#final stage
FROM alpine:latest
RUN apk --no-cache add ca-certificates
COPY --from=builder /go/bin/app /app
ENV GIN_MODE release
ENTRYPOINT /app
LABEL Name=goapp Version=1.0
EXPOSE 8080
