# Systemd service for MinIO

Systemd script for Distributed MinIO server.

## Installation

- Systemd script is configured to run the binary from /usr/local/bin/.
- Systemd script is configured to run the binary as `minio-user`, make sure you create this user prior using service script.
- Download the binary. Find the relevant links for the binary at https://min.io/download#/linux.

## Create the Environment configuration file

This file serves as input to MinIO systemd service. Use this file to add `MINIO_VOLUMES` with the correct paths, `MINIO_OPTS` to add MinIO server options like `certs-dir`, `address`. MinIO credentials should be `MINIO_ROOT_USER` and `MINIO_ROOT_PASSWORD` in this file as well.

```sh
$ cat <<EOT >> /etc/default/minio
# Remote volumes to be used for MinIO server.
MINIO_VOLUMES=http://node{1...6}/export{1...32}
# Use if you want to run MinIO on a custom port.
MINIO_OPTS="--address :9199 --console-address :9001"
# Root user for the server.
MINIO_ROOT_USER=Root-User
# Root secret for the server.
MINIO_ROOT_PASSWORD=Root-Password
EOT
```

For distributed setup it is required to copy this file across all nodes to have consistent credentials.

## Systemctl

Download `minio.service` in  `/etc/systemd/system/`

```
( cd /etc/systemd/system/; curl -O https://raw.githubusercontent.com/minio/minio-service/master/linux-systemd/distributed/minio.service )
```

Enable startup on boot

```
systemctl enable minio.service
```

## Note

- Replace ``User=minio-user`` and ``Group=minio-user`` in minio.service file with your local setup.
- Ensure that ``MINIO_VOLUMES`` source has appropirate write access.
