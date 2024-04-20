# How to Use the Binary Distribution of OPC UA Device Support

## Contents

The Binary Distribution tar contains two EPICS applications:

**opcuaBinaryDist** -
EPICS Support Module that contains the binary distribution,
in form of a shared library that embeds (contains)
the Unified Automation low-level client library.
In your installation,
this module can be used like any other EPICS Support Module.

**opcuaExampleIoc** -
A copy of the OPC UA Device Support example application,
which can be build using the binary distribution
and provides an IOC binary as well as two IOC setups
to connect to a Siemens S7-1500 PLC
or to the Unified Automation example server.

The low-level client inside the binary distribution
is fully functional
and can be used without limitations or any fees.

## Caveat

You need to download the binary distribution tar
that matches your Linux distribution and the exact EPICS Base version,
else you will not be able to create (link) IOCs.

You do not need to checkout, download or compile
the OPC UA Device Support module or a low-level client library.
The *opcuaBinaryDist* module replaces both.
You only need the matching EPICS Base
and an application that builds an IOC, like the *opcuaExampleIoc* module.

## Building

Here's a log of building these two modules on a blank CentOS 7 machine.
I used a container to create this log, that's why the commands are run as root.
You should use your own account instead.

```
# OPC UA Support
# binary distribution build using s blank CentOS7 container

# Inside the container, I am building as root (as there are no users).
# On a real system, only run the "yum" commands as root.
# Download and build everything else under your own user name.

langer @ DOCKER-84MRG : ~ $ podman run -it centos:7

# Create a working dir under /root (home of root)

[root@6a1fae9c8335 /]# cd
[root@6a1fae9c8335 ~]# mkdir epics-opcua
[root@6a1fae9c8335 ~]# cd epics-opcua

# Install prerequisites for EPICS Base

[root@6a1fae9c8335 epics-opcua]# yum -y install gcc-c++ perl make libreadline

# Install prerequisites for OPC UA binary distribution

[root@6a1fae9c8335 epics-opcua]# yum -y install openssl-devel libxml2-devel

# Download, unpack, configure and build EPICS Base

[root@6a1fae9c8335 epics-opcua]# curl -OL https://epics-controls.org/download/base/base-7.0.8.tar.gz
[root@6a1fae9c8335 epics-opcua]# tar xzf base-7.0.8.tar.gz
[root@6a1fae9c8335 epics-opcua]# cat > base-7.0.8/configure/CONFIG_SITE.local <<EOF
> USR_CXXFLAGS_Linux += -std=c++11
> EOF
[root@6a1fae9c8335 epics-opcua]# make -j2 -C base-7.0.8

# Download, unpack, configure and build the OPC UA Binary Distribution

[root@6a1fae9c8335 epics-opcua]# curl -OL https://github.com/epics-modules/opcua/releases/download/v0.9.5/BDIST_opcua-0.9.5_Base-7.0.8_rhel7.tar.gz
[root@6a1fae9c8335 epics-opcua]# tar xzf BDIST_opcua-0.9.5_Base-7.0.8_rhel7.tar.gz
[root@6a1fae9c8335 epics-opcua]# cat > RELEASE.local <<EOF
> EPICS_BASE = /root/epics-opcua/base-7.0.8
> OPCUA = /root/epics-opcua/opcuaBinaryDist
> EOF
[root@6a1fae9c8335 epics-opcua]# make -j2 -C opcuaBinaryDist
[root@6a1fae9c8335 epics-opcua]# make -j2 -C opcuaExampleIoc

# Run the OPC UA Example IOC

[root@6a1fae9c8335 epics-opcua]# ( cd opcuaExampleIoc/iocBoot/iocS7-1500/; ../../bin/linux-x86_64/opcuaIoc st.cmd )
#!../../bin/linux-x86_64/opcuaIoc
## You may have to change opcuaIoc to something else
## everywhere it appears in this file
< envPaths
epicsEnvSet("IOC","iocS7-1500")
epicsEnvSet("TOP","/root/epics-opcua/opcuaExampleIoc")
epicsEnvSet("OPCUA","/root/epics-opcua/opcuaBinaryDist")
epicsEnvSet("EPICS_BASE","/root/epics-opcua/base-7.0.8")
cd "/root/epics-opcua/opcuaExampleIoc"
## Register all support components
dbLoadDatabase "dbd/opcuaIoc.dbd"
opcuaIoc_registerRecordDeviceDriver pdbbase
## Pretty minimal setup: one session with a 200ms subscription on top
opcuaSession OPC1 opc.tcp://localhost:4840
opcuaSubscription SUB1 OPC1 200
# Switch off security
opcuaOptions OPC1 sec-mode=None
## Load the databases for one of the examples
## Siemens S7-1500 PLC
dbLoadRecords "db/S7-1500-server.db", "P=OPC:,R=,SESS=OPC1,SUBS=SUB1"
dbLoadRecords "db/S7-1500-DB1.db", "P=OPC:,R=DB1:,SESS=OPC1,SUBS=SUB1"
iocInit
Starting iocInit
############################################################################
## EPICS R7.0.8
## Rev. 2024-04-20T10:07+0000
## Rev. Date build date/time:
############################################################################
OPC UA Client Device Support 0.9.5 (-); using Unified Automation C++ Client SDK v1.7.4-520
iocRun: All initialization complete
OPC UA: Autoconnecting sessions
## Start any sequence programs
#seq sncopcuaIoc,"user=ralph"
epics>
```