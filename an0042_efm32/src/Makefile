PACKAGE = bootloader

SDK_PATH  ?= Gecko_SDK

TRGT      ?= arm-none-eabi-
CC         = $(TRGT)gcc
CXX        = $(TRGT)g++
OBJCOPY    = $(TRGT)objcopy

VPATH    =  $(SDK_PATH)/Device/SiliconLabs/EFM32HG/Source/ \
            $(SDK_PATH)/Device/SiliconLabs/EFM32HG/Source/GCC \
			$(SDK_PATH)/middleware/usb_gecko/src/ \
			$(SDK_PATH)/emlib/src/

LDSCRIPT = $(SDK_PATH)/Device/SiliconLabs/EFM32HG/Source/GCC/efm32hg.ld

INC = 
INC += -I.
INC += -I$(SDK_PATH)/Device/SiliconLabs/EFM32HG/Include
INC += -I$(SDK_PATH)/CMSIS/Include
INC += -I$(SDK_PATH)/emlib/inc
INC += -I$(SDK_PATH)/middleware/usb_gecko/inc

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

LIBS = $(SDK_PATH)/CMSIS/Lib/GCC/libarm_cortexM0l_math.a -lgcc

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
	system_efm32hg.c \
	em_usbd.c \
	em_usbdch9.c \
	em_usbdep.c \
	em_usbdint.c \
	em_usbhal.c \
	em_usbtimer.c \
	em_core.c \
	em_cmu.c \
	em_emu.c \
	em_system.c \
	em_timer.c

SRC_A = \
	startup_efm32hg.S

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
	$(QUIET) rm -rf $(SDK_PATH)
	$(QUIET) rm -f $(wildcard $(OBJ_DIR)/*.o)
	$(QUIET) rm -f $(TARGET) $(PACKAGE).bin $(PACKAGE).symbol

deps:
	@if [ ! -d $(SDK_PATH) ]; then \
		wget https://devtools.silabs.com/studio/v4/updates/binary/com.silabs.sdk.gecko_platform.v2.1.feature_root_2.1.0.201712201229-105 -O sdk.zip; \
		unzip sdk.zip 'developer/sdks/gecko_sdk_suite/v2.1/platform/*' -d $(SDK_PATH)_tmp ; \
		mv $(SDK_PATH)_tmp/developer/sdks/gecko_sdk_suite/v2.1/platform $(SDK_PATH); \
		rm -rf $(SDK_PATH)_tmp; \
		rm -f sdk.zip; \
	fi


