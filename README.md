# OPC UA Binary Builder Recipe for Debian / Red Hat / Ubuntu

# Idea

To facilitate fast and easy building of the OPC UA binary distribution,
OCI containers are used.

For each Linux distribution/release, a single build container image is
created. This container image contains several versions of EPICS Base and
the other dependencies as well as the Unified Automation SDK C++ Client
library, completely built for the Linux distro/release.

A build script is also copied into the container, which downloads and
builds exactly one version of the OPC UA Device Support against all
versions of EPICS Base in the image (or a subset of them).

When the container is started (the build script is the default entry
point), the binary distributions are built and the results (distribution
tar archives) are written into the bind-mounted `/result` directory
outside of the container.

These instructions assume the use of `podman` with rootless containers.

## Identify sources and set up the environment

Identify the tar of the Unified Automation SDK C++ Client library.
Copy it to the build directory (where the Dockerfiles are).

## Build the container image

Note:
The setting of `EPICS_VERSIONS` (defaults to the last public 3.15 and
EPICS 7 releases) determines the different versions of EPICS Base that
the image can be used for. This setting can be overwritten
when running the container to only build for a subset of versions.

The build command for e.g. Debian 11 would be:

```
$ podman build -t opcua-debian11 \
--build-arg RELEASE=11 \
--build-arg VCS_VERSION=$(git describe --always --tags --dirty) \
-f Dockerfile.debian
```

Other OS/release combinations can be selected through specifying
the `RELEASE` argument and the Dockerfile.

Supported and tested combinations:
| Dockerfile           | RELEASE | Artifact Tag | Note               |
|----------------------|---------|--------------|--------------------|
| Dockerfile.debian    | 10      | debian10     |                    |
| Dockerfile.debian    | 11      | debian11     |                    |
| Dockerfile.centos    | 7       | rhel7        |                    |
| Dockerfile.almalinux | 8       | rhel8        |                    |
| Dockerfile.almalinux | 9       | rhel9        | no OPC UA Security |
| Dockerfile.ubuntu    | 20.04   | ubuntu20.04  |                    |
| Dockerfile.ubuntu    | 22.04   | ubuntu22.04  | no OPC UA Security |

RHEL 9 and Ubuntu 22 have bumped the version of the SSL library to
3.x, which is not compatible with the Unified Automation SDK 1.7.4
that I have access to.

## Run the image to build the binary distribution

When running the container, bind-mount a volume to /result.
The resulting distribution tars will be written there.

You can set EPICS_VERSIONS to a subset of the versions inside the image
to only build for a subset of EPICS versions.

The simplest execution line for the container image mentioned above
would be:

```
$ podman run -it --rm -v ./results/:/result opcua-debian11
```
