# OPC UA Binary Builder Recipe for Debian / Red Hat

These instructions assume the use of `podman` with rootless containers.

## Identify sources and set up the environment

Identify the tar of the Unified Automation SDK C++ Client library.
Copy or hard link it to the build directory (where the Dockerfile is).

## Build the container image

Building the SDK and the EPICS related modules is part of creating the 
container image.

The OPC UA related modules will be (re)built when running the container.
The same image can be run to build different OPC UA versions.

Note:
The setting of `EPICS_VERSIONS` (defaults to the last public 3.15 and
EPICS 7 releases) determines the different versions of EPICS Base that
the image can be used for. This setting can be overwritten
when running the comtainer to only build for a subset of versions.

```
$ podman build -t opcua-deb11:in-work --build-arg RELEASE=11 debian
```

## Run the image to build the binary distribution

When running the container, bind-mount a volume to /result. The resulting
distribution tars will be written there.

You can set EPICS_VERSIONS to a subset of the versions inside the image
to only build for a subset of EPICS versions.

```
$ podman run -it --rm -v ./results/:/result opcua-deb11
```
