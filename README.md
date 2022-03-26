# OPC UA Binary Builder Recipe for Debian / Red Hat

These instructions assume the use of `podman` with rootless containers.

## Identify sources and set up the environment

Identify the tar of the Unified Automation SDK C++ Client library and set 
the environment variable `SDK_TAR` to its fully qualified name.

Set the environment variable `BASE_VERSIONS` to the release numbers of
the versions of EPICS Base you want create the binary distribution for.

    ```
	$ export SDK_TAR=/path/to/my/SDK/uasdkcppclient-src-debian9.13-x86_64-gcc6.3.0-v1.7.4-520.tar.gz
	$ export BASE_VERSION="3.15.9 7.0.6.1"
	```
	
## Build the containers

The OPC UA related modules will be (re)built when running the containers
to allow reusing the images for different OOC UA versions.
Building the SDK and the EPICS related modules is part of creating the 
container image.





Create a file system tar from a VM

- On kickstarted machine, as root:

1. Make sure only one kernel version is installed

2. Remove packages not needed on virtualized systems
    ```
    # dnf remove -y *firmware* splunkforwarder microcode*
    # rm /etc/init.d/splunk
    ```

