FROM golang:1.10 as build-env

RUN go get github.com/derekparker/delve/cmd/dlv

FROM gcr.io/distroless/base
COPY --from=build-env /go/bin/dlv /

CMD ["/dlv"]

