# Create Container Disk Image from QCOW2 or Raw Image

## Download QCOW2 image

```bash
curl -L 'https://download.fedoraproject.org/pub/fedora/linux/releases/42/Cloud/x86_64/images/Fedora-Cloud-Base-Generic-42-1.1.x86_64.qcow2' -O
curl -L 'https://download.fedoraproject.org/pub/fedora/linux/releases/42/Cloud/x86_64/images/Fedora-Cloud-42-1.1-x86_64-CHECKSUM' -O
sha256sum --ignore-missing -c Fedora-Cloud-42-1.1-x86_64-CHECKSUM
```
>**NOTE:** Validate image and checksum file to ensure integrity of workflow.

## Create Containerfile

Create Containerfile:

```bash
cat > Containerfile << EOF
FROM registry.access.redhat.com/ubi10/ubi:latest AS builder
ADD --chown=107:107 Fedora-Cloud-Base-Generic-42-1.1.x86_64.qcow2 /disk/
RUN chmod 0440 /disk/*

FROM scratch
COPY --from=builder /disk/* /disk/

EOF
```

## Build And Tag The Container:

Build and tag image:
```bash
podman build -t infra-services.tkagn.io:5000/containerdisks/fedora:42 .
```

## Push The Image To The Registry (Quay)

Push image:

```bash
podman push infra-services.tkagn.io:5000/fedora/fedora:42
```

## Pull Image From Registry (Quay)

```bash
podman pull infra-services.tkagn.io:5000/fedora/fedora:42
podman image mount infra-services.tkagn.io:5000/fedora/fedora:42
cp /var/lib/containers/storage/overlay/<Directory from previous command> ./Fedora42.qcow2
podman image umount infra-services.tkagn.io:5000/fedora/fedora:42
```
