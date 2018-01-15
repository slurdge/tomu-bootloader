PACKAGE = bootloader

TRGT      ?= arm-none-eabi-
CC         = $(TRGT)gcc
CXX        = $(TRGT)g++
OBJCOPY    = $(TRGT)objcopy

VPATH    = ../EFM32HG/Source/ ../EFM32HG/Source/GCC/ ../usb_gecko/src/ ../emlib/src/

LDSCRIPT = ../EFM32HG/Source/GCC/efm32hg.ld

INC = 
INC += -I.
INC += -I../EFM32HG/Include
INC += -I../CMSIS/Include
INC += -I../emlib/inc
INC += -I../usb_gecko/inc

OBJ_DIR = .obj

CFLAGS_CORTEX_M = -mthumb -mfloat-abi=soft -mcpu=cortex-m0plus

CFLAGS = $(INC) -Wall -Wpointer-arith -Werror -std=gnu99 $(CFLAGS_CORTEX_M) $(CFLAGS_EXTRA)

CFLAGS += -fno-builtin \
          -ffreestanding -ffunction-sections -fdata-sections -fno-common \
          -fomit-frame-pointer -falign-functions=16 -nostdlib -Os

CFLAGS += -D__START=main -D__NO_SYSTEM_INIT -DEFM32HG309F64 -DNO_RAMFUNCS

LDFLAGS     = $(ADD_LFLAGS) $(CFLAGS) \
             -nostartfiles -nostdlib -nodefaultlibs \
             -Wl,--gc-sections \
             -Wl,--no-warn-mismatch,--script=$(LDSCRIPT),--build-id=none

LIBS = ../CMSIS/Lib/GCC/libarm_cortexM0l_math.a -lgcc

SRC_C = \
	autobaud.c \
	boot.c \
	bootldio.c \
	cdc.c \
	crc.c \
	flash.c \
	main.c \
	retargetdebug.c \
	xmodem.c

SRC_SL = \
	../EFM32HG/Source/system_efm32hg.c \
	../usb_gecko/src/em_usbd.c \
	../usb_gecko/src/em_usbdch9.c \
	../usb_gecko/src/em_usbdep.c \
	../usb_gecko/src/em_usbdint.c \
	../usb_gecko/src/em_usbhal.c \
	../usb_gecko/src/em_usbtimer.c \
	../emlib/src/em_core.c \
	../emlib/src/em_cmu.c \
	../emlib/src/em_emu.c \
	../emlib/src/em_system.c \
	../emlib/src/em_timer.c

SRC_A = \
	../EFM32HG/Source/GCC/startup_efm32hg.S

CSOURCES   = $(SRC_C) $(SRC_SL)
CPPSOURCES = 
ASOURCES   = $(SRC_A)
COBJS      = $(addprefix $(OBJ_DIR)/, $(notdir $(CSOURCES:.c=.o)))
CXXOBJS    = $(addprefix $(OBJ_DIR)/, $(notdir $(CPPSOURCES:.cpp=.o)))
AOBJS      = $(addprefix $(OBJ_DIR)/, $(notdir $(ASOURCES:.S=.o)))
OBJECTS = $(COBJS) $(CXXOBJS) $(AOBJS)

ALL        = all
TARGET     = $(PACKAGE).elf
CLEAN      = clean

$(ALL): $(TARGET)

$(OBJ_DIR):
	$(QUIET) mkdir $(OBJ_DIR)

$(TARGET): $(OBJECTS) $(LDSCRIPT)
	$(QUIET) echo "  LD       $@"
	$(QUIET) $(CC) $(OBJECTS) $(CFLAGS) $(LDFLAGS) $(LIBS)  -o $@
	$(QUIET) echo "  OBJCOPY  $(PACKAGE).bin"
	$(QUIET) $(OBJCOPY) -O binary $@ $(PACKAGE).bin

$(COBJS) : $(OBJ_DIR)/%.o : %.c Makefile
	$(QUIET) echo "  CC       $<	$(notdir $@)"
	$(QUIET) $(CC) -c $< $(CFLAGS) -o $@

$(OBJ_DIR)/%.o: %.cpp
	$(QUIET) echo "  CXX      $<	$(notdir $@)"
	$(QUIET) $(CXX) -c $< $(CXXFLAGS) -o $@

$(OBJ_DIR)/%.o: %.S
	$(QUIET) echo "  AS       $<	$(notdir $@)"
	$(QUIET) $(CC) -x assembler-with-cpp -c $< $(CFLAGS) -o $@

.PHONY: $(CLEAN)

$(CLEAN):
	$(QUIET) rm -f $(wildcard $(OBJ_DIR)/*.o)
	$(QUIET) rm -f $(TARGET) $(PACKAGE).bin $(PACKAGE).symbol

.PHONY: all