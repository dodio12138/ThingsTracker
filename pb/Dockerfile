ARG POCKETBASE_VERSION=0.22.11
FROM alpine:3.20

RUN apk add --no-cache ca-certificates unzip wget

WORKDIR /pb

RUN wget -q https://github.com/pocketbase/pocketbase/releases/download/v${POCKETBASE_VERSION}/pocketbase_${POCKETBASE_VERSION}_linux_amd64.zip -O /tmp/pb.zip \
  && unzip /tmp/pb.zip -d /pb \
  && rm /tmp/pb.zip \
  && chmod +x /pb/pocketbase

EXPOSE 8090
CMD ["/pb/pocketbase", "serve", "--http=0.0.0.0:8090", "--dir=/pb/pb_data"]
