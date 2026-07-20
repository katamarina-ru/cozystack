ARG builder_image=docker.io/library/golang:1.26@sha256:3aff6657219a4d9c14e27fb1d8976c49c29fddb70ba835014f477e1c70636647
FROM ${builder_image} AS builder

ARG TARGETOS
ARG TARGETARCH

WORKDIR /src

COPY go.mod go.sum ./
RUN go mod download

COPY *.go ./
RUN CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build \
    -ldflags "-X kubevirt.io/csi-driver/pkg/service.VendorVersion=0.2.0" \
    -o kubevirt-csi-driver .

FROM quay.io/centos/centos:stream9@sha256:a93e3316b14a226863327f35110909569ec554fc9e092bc307282cf00e17e3d0

RUN dnf install -y e2fsprogs xfsprogs nfs-utils && dnf clean all

COPY --from=builder /src/kubevirt-csi-driver .

ENTRYPOINT ["./kubevirt-csi-driver"]
