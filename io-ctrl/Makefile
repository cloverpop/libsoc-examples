# file      Makefile
# copyright Copyright (c) 2014 Toradex AG
#           [Software License Agreement]
# author    $Author$
# version   $Rev$
# date      $Date$
# brief     a simple makefile to (cross) compile.
#           uses the openembedded provided sysroot and toolchain
# target    linux on Colibri T20 / Colibri T30 / Colibri VF50 / Colibri VF61 / Apalis T30 / Apalis IMX6Q/D
# caveats   -

##############################################################################
# Setup your project settings
##############################################################################

# Set the input source files, the binary name and used libraries to link
SRCS = io.o
PROG := io
LIBS = -lm -lpthread

# Set arch flags
ARCH_CFLAGS = -march=armv7-a -fno-tree-vectorize -mthumb-interwork -mfloat-abi=hard

ifeq ($(MACHINE), colibri-t20)
  ARCH_FLAGS += -mfpu=vfpv3-d16 -mtune=cortex-a9
else ifeq ($(MACHINE), colibri-t30)
  ARCH_FLAGS += -mfpu=neon -mtune=cortex-a9
else ifeq ($(MACHINE), colibri-vf)
  ARCH_FLAGS += -mfpu=neon -mtune=cortex-a5
else ifeq ($(MACHINE), apalis-t30)
  ARCH_FLAGS += -mfpu=neon -mtune=cortex-a9
else ifeq ($(MACHINE), apalis-imx6)
  ARCH_FLAGS += -mfpu=neon -mtune=cortex-a9
  CFLAGS += -DAPALIS_IMX6
else ifeq ($(MACHINE), colibri-imx6)
  ARCH_FLAGS += -mfpu=neon -mtune=cortex-a9
  CFLAGS += -DCOLIBRI_IMX6
else ifeq ($(MACHINE), colibri-imx7)
  ARCH_FLAGS += -mfpu=neon -mtune=cortex-a7
  CFLAGS += -DCOLIBRI_IMX7
else ifeq ($(MACHINE), apalis-tk1)
  ARCH_FLAGS += -mfpu=neon -mtune=cortex-a15
  CFLAGS += -DAPALIS_TK1
else
  $(error MACHINE is not set to a valid target)
endif

# Set flags to the compiler and linker
CFLAGS += -O2 -g -Wall -DNV_IS_LDK=1 $(ARCH_CFLAGS) `$(PKG-CONFIG) --cflags libsoc` --sysroot=$(OECORE_TARGET_SYSROOT) -I$(OECORE_TARGET_SYSROOT)include
#LDFLAGS += $(OECORE_TARGET_SYSROOT)/lib/libsoc.a
LDFLAGS += `$(PKG-CONFIG) --libs libsoc`

##############################################################################
# Setup your build environment
##############################################################################

# Set the path to the oe built sysroot and
# Set the prefix for the cross compiler
OECORE_NATIVE_SYSROOT ?= /usr/local/oecore-x86_64/sysroots/x86_64-angstromsdk-linux/
OECORE_TARGET_SYSROOT ?= /usr/local/oecore-x86_64/sysroots/armv7at2hf-neon-angstrom-linux-gnueabi/
CROSS_COMPILE ?= $(OECORE_NATIVE_SYSROOT)usr/bin/arm-angstrom-linux-gnueabi/arm-angstrom-linux-gnueabi-

##############################################################################
# The rest of the Makefile usually needs no change
##############################################################################

# Set differencies between native and cross compilation
ifneq ($(strip $(CROSS_COMPILE)),)
  LDFLAGS += -L$(OECORE_TARGET_SYSROOT)usr/lib -Wl,-rpath-link,$(OECORE_TARGET_SYSROOT)usr/lib -L$(OECORE_TARGET_SYSROOT)lib -Wl,-rpath-link,$(OECORE_TARGET_SYSROOT)lib
  BIN_POSTFIX =
  PKG-CONFIG = export PKG_CONFIG_SYSROOT_DIR=$(OECORE_TARGET_SYSROOT); \
               export PKG_CONFIG_PATH=$(OECORE_TARGET_SYSROOT)usr/lib/pkgconfig/; \
               $(OECORE_NATIVE_SYSROOT)usr/bin/pkg-config
else
# Native compile
  PKG-CONFIG = pkg-config
  ARCH_CFLAGS =
# Append .x86 to the object files and binaries, so that native and cross builds can live side by side
  BIN_POSTFIX = .x86
endif

# Toolchain binaries
CC = $(CROSS_COMPILE)gcc
LD = $(CROSS_COMPILE)ld
STRIP = $(CROSS_COMPILE)strip
RM = rm -f

# Sets the output filename and object files
PROG := $(PROG)$(BIN_POSTFIX)
OBJS = $(SRCS:.c=$(BIN_POSTFIX).o)
DEPS = $(OBJS:.o=.o.d)

# pull in dependency info for *existing* .o files
-include $(DEPS)

.DEFAULT_GOAL := all

all: $(PROG)

$(PROG): $(OBJS) Makefile
	$(CC) $(CFLAGS) $(OBJS) $(LIBS) $(LDFLAGS) -o $@
	$(STRIP) $@

%$(BIN_POSTFIX).o: %.c
	$(CC) -c $(CFLAGS) -o $@ $<
	$(CC) -MM $(CFLAGS) $< > $@.d

clean:
	$(RM) $(OBJS) $(PROG) $(DEPS)

.PHONY: all clean
