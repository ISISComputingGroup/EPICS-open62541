TOP = ../..
include $(TOP)/configure/CONFIG

## setting MAKEFLAGS causes cmake issues
override MAKEFLAGS=

CMAKE ?= cmake

# extract visual studio major version number from path
# unfortunately cannot use %.% so using %.0 but may not match minor version number changes e.g. 10.1
ifneq ($(VisualStudioVersion),)
VS_VERS=$(filter %.0,$(subst \, ,$(VisualStudioVersion)))
else
VS_VERS=$(filter %.0,$(subst \, ,$(VSINSTALLDIR)))
endif
VS_MAJOR=$(word 1,$(subst ., ,$(VS_VERS)))

MYINSTALLDIR=$(abspath $(TOP))/install/$(EPICS_HOST_ARCH)
CMAKE_CONFIG_FLAGS += -DCMAKE_INSTALL_PREFIX=$(MYINSTALLDIR)

ifeq ($(findstring static,$(EPICS_HOST_ARCH)),)
CMAKE_CONFIG_FLAGS += -DBUILD_SHARED_LIBS=ON
else
CMAKE_CONFIG_FLAGS += -DBUILD_SHARED_LIBS=OFF
endif

CMAKE_CONFIG_FLAGS += -DUA_ENABLE_ENCRYPTION=OPENSSL -DOPENSSL_ROOT_DIR=$(OPENSSL)/install/$(EPICS_HOST_ARCH)

CMAKE_BUILD_FLAGS=
ifneq ($(findstring linux,$(EPICS_HOST_ARCH)),)
CMAKE_GENERATOR=Unix Makefiles
else
ifneq ($(findstring windows,$(EPICS_HOST_ARCH)),)
VS_ARCH=$(VS_MAJOR)
PLATFORM=x64
else
VS_ARCH=$(VS_MAJOR)
PLATFORM=Win32
endif
CMAKE_GENERATOR=Visual Studio $(VS_ARCH)
endif

ifneq ($(findstring debug,$(EPICS_HOST_ARCH)),)
CMAKE_CONFIG=Debug
else
CMAKE_CONFIG=RelWithDebInfo
endif
CMAKE_CONFIG_FLAGS += -DCMAKE_BUILD_TYPE:STRING=$(CMAKE_CONFIG)

include $(TOP)/configure/RULES
#----------------------------------------
#  ADD RULES AFTER THIS LINE

ifdef T_A
install:
	$(CMAKE) $(TOP) -G "$(CMAKE_GENERATOR)" -A "$(PLATFORM)" $(CMAKE_CONFIG_FLAGS)
	$(CMAKE) --build . --target install --config $(CMAKE_CONFIG) $(CMAKE_BUILD_FLAGS)
	-$(MKDIR) $(TOP)/bin
	-$(MKDIR) $(TOP)/bin/$(EPICS_HOST_ARCH)
	-$(MKDIR) $(TOP)/lib
	-$(MKDIR) $(TOP)/lib/$(EPICS_HOST_ARCH)
ifeq ($(findstring linux,$(EPICS_HOST_ARCH)),)
	$(CP) -f $(MYINSTALLDIR)/bin/*.dll $(TOP)/bin/$(EPICS_HOST_ARCH)/
	$(CP) -f $(MYINSTALLDIR)/lib/*.lib $(TOP)/lib/$(EPICS_HOST_ARCH)/
endif
#ifeq ($(findstring linux,$(EPICS_HOST_ARCH)),)
#ifeq ($(findstring linux,$(EPICS_HOST_ARCH)),)
#	$(CP) -f $(MYSQLCONN_DIR)/bin/* $(TOP)/bin/$(EPICS_HOST_ARCH)/
# dlls and pdbs need to be in bin not lib (and leaving copy in lib causes link errors)
#	$(CP) -f $(MYSQLCONN_DIR)/lib64/$(INSTSUBDIR)/* $(TOP)/lib/$(EPICS_HOST_ARCH)/
#	$(CP) -f $(MYSQLCONN_DIR)/lib64/vs14/$(INSTSUBDIR)/mysqlcppconn$(MYSQLLIBSFX).lib $(TOP)/lib/$(EPICS_HOST_ARCH)/mysqlcppconn.lib
#	$(CP) -f $(MYSQLCONN_DIR)/lib64/vs14/$(INSTSUBDIR)/mysqlcppconn8$(MYSQLLIBSFX).lib $(TOP)/lib/$(EPICS_HOST_ARCH)/mysqlcppconn8.lib
#	$(CP) -f $(MYSQLCONN_DIR)/lib64/$(INSTSUBDIR)/*.dll $(TOP)/bin/$(EPICS_HOST_ARCH)/
#	$(CP) -f $(MYSQLCONN_DIR)/lib64/$(INSTSUBDIR)/*.pdb $(TOP)/bin/$(EPICS_HOST_ARCH)/
#else
#	find $(MYSQLCONN_DIR)/lib* \( -type f -o -type l \) -exec $(CP) {} $(TOP)/lib/$(EPICS_HOST_ARCH)/ \;
#	find $(MYSQLCONN_DIR)/lib* -name libmysqlcppconn-static.a -exec $(CP) {} $(TOP)/lib/$(EPICS_HOST_ARCH)/libmysqlcppconn.a \;
#	find $(MYSQLCONN_DIR)/lib* -name libmysqlcppconn8-static.a -exec $(CP) {} $(TOP)/lib/$(EPICS_HOST_ARCH)/libmysqlcppconn8.a \;
#	$(CP) -f $(MYSQL_INSTALL_DIR)/lib64/mysql/libmysqlclient.so  $(TOP)/lib/$(EPICS_HOST_ARCH)/
#endif
#endif
#	-$(MKDIR) $(TOP)/include
#	-$(MKDIR) $(TOP)/include/cppconn
#	$(CP) -f $(MYSQLCONN_DIR)/include/jdbc/*.h $(TOP)/include/
#	$(CP) -f $(MYSQLCONN_DIR)/include/jdbc/cppconn/*.h $(TOP)/include/cppconn/
endif
