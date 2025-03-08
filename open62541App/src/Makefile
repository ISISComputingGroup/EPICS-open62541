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

#CMAKE_CONFIG_FLAGS += -DUA_BUILD_EXAMPLES=ON
#CMAKE_CONFIG_FLAGS += -DUA_BUILD_TOOLS=ON
#CMAKE_CONFIG_FLAGS += -DUA_BUILD_UNIT_TESTS=ON

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
CMAKE_CONFIG_FLAGS += -DUA_BUILD_TOOLS:BOOL=ON
CMAKE_CONFIG_FLAGS += -DUA_BUILD_EXAMPLES:BOOL=ON
#CMAKE_CONFIG_FLAGS += -DUA_NAMESPACE_ZERO:STRING=FULL

# ZLIB defined in configure/RELEASE and used in CMakeLists.txt
export ZLIB

include $(TOP)/configure/RULES
#----------------------------------------
#  ADD RULES AFTER THIS LINE

ifdef T_A
install:
	$(CMAKE) $(TOP) -G "$(CMAKE_GENERATOR)" -A "$(PLATFORM)" $(CMAKE_CONFIG_FLAGS)
	$(CMAKE) --build . --target install --config $(CMAKE_CONFIG) $(CMAKE_BUILD_FLAGS)
ifneq ($(findstring linux-x86_64,$(EPICS_HOST_ARCH)),)
	$(CMAKE) -E copy_directory $(MYINSTALLDIR)/lib64 $(TOP)/lib/$(EPICS_HOST_ARCH)
else
ifeq ($(findstring static,$(EPICS_HOST_ARCH)),)
	$(CMAKE) -E copy_directory $(MYINSTALLDIR)/bin $(TOP)/bin/$(EPICS_HOST_ARCH)
endif
	$(CMAKE) -E copy_directory $(MYINSTALLDIR)/lib $(TOP)/lib/$(EPICS_HOST_ARCH)
	$(CP) bin/$(CMAKE_CONFIG)/ua.exe $(TOP)/bin/$(EPICS_HOST_ARCH)
	$(CP) bin/examples/client.exe $(TOP)/bin/$(EPICS_HOST_ARCH)
endif
# we cannot do this as include already exists as part of source tree
# so EPICS OPCUA will need to use $(MYINSTALLDIR)/include 
#	$(CMAKE) -E copy_directory $(MYINSTALLDIR)/include $(TOP)/include
endif
