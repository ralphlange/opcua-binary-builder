TOP=..
include $(TOP)/configure/CONFIG
#----------------------------------------
#  ADD MACRO DEFINITIONS AFTER THIS LINE
#=============================

#=============================
# Install the distribution

DBD += opcua.dbd
DBD += devOpcua.dbd
DBD += opcuaItemRecord.dbd
DBD += menuDefAction.dbd

CFG += CONFIG_OPCUA

SHRLIB_VERSION = _SHRLIB_VERSION_
LIB_INSTALLS += ../$(SHRLIB_PREFIX)opcua$(SHRLIB_SUFFIX)

#===========================

include $(TOP)/configure/RULES
#----------------------------------------
#  ADD RULES AFTER THIS LINE

buildInstall:
	rm -f $(INSTALL_SHRLIB)/$(SHRLIB_PREFIX)opcua$(SHRLIB_SUFFIX_BASE)
	ln -s $(SHRLIB_PREFIX)opcua$(SHRLIB_SUFFIX) $(INSTALL_SHRLIB)/$(SHRLIB_PREFIX)opcua$(SHRLIB_SUFFIX_BASE)
