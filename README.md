# OPC UA Binary Builder Recipe for Debian / Red Hat

# Idea

To facilitate fast and easy building of the OPC UA binary distribution,
OCI containers are used.

For each Linux distribution/release a single build container image is
created. This container image contains several versions of EPICS Base and
the oder dependencies as well as the Unified Automation SDK C++ Client
library, completely built for this Linux distro/release.

A build script is also copied into the container, which downloads and
builds exactly one version of the OPC UA Device Support against all
versions of EPICS Base in the image (or a subset).

When the container is started (the build script is the default entry
point), the binary distributions are built and the results (distribution
tar archives) are written into the bind-mounted `/result` directory
outside of the container.

These instructions assume the use of `podman` with rootless containers.

## Identify sources and set up the environment

Identify the tar of the Unified Automation SDK C++ Client library.
Link it to the build directory (where the Dockerfiles are).

## Build the container image

Note:
The setting of `EPICS_VERSIONS` (defaults to the last public 3.15 and
EPICS 7 releases) determines the different versions of EPICS Base that
the image can be used for. This setting can be overwritten
when running the container to only build for a subset of versions.

The build command for e.g. Debian 11 would be:

```
$ podman build -t opcua-deb11:in-work \
--build-arg RELEASE=11 \
--build-arg VCS_VERSION=$(git describe --always --tags --dirty) \
-f Dockerfile.debian
```

Other OS/release combinations can be selected through specifying
the `RELEASE` argument and the Dockerfile.

Supported and tested combinations:
| Dockerfile           | RELEASE | Artifact Tag |
|----------------------|---------|--------------|
| Dockerfile.debian    | 10      | deb10        |
| Dockerfile.debian    | 11      | deb11        |
| Dockerfile.centos    | 7       | rhel7        |
| Dockerfile.almalinux | 8       | rhel8        |

## Run the image to build the binary distribution

When running the container, bind-mount a volume to /result.
The resulting distribution tars will be written there.

You can set EPICS_VERSIONS to a subset of the versions inside the image
to only build for a subset of EPICS versions.

The simplest execution line for the container image mentioned above
would be:

```
$ podman run -it --rm -v ./results/:/result opcua-deb11:in-work
```
