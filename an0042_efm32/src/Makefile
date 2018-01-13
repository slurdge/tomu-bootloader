TRGT      ?= arm-none-eabi-
CC         = $(TRGT)gcc
CXX        = $(TRGT)g++
OBJCOPY    = $(TRGT)objcopy

INC = 
INC += -I.
INC += -I../EFM32HG/Include
INC += -I../CMSIS/Include
INC += -I../emlib/inc
INC += -I../usb_gecko/inc

CFLAGS_CORTEX_M = -mthumb -mfloat-abi=soft -mcpu=cortex-m0plus

CFLAGS = $(INC) -Wall -Wpointer-arith -Werror -std=gnu99 -nostdlib $(CFLAGS_CORTEX_M) $(CFLAGS_EXTRA)

CFLAGS += -fdata-sections -ffunction-sections -DEFM32HG309F64 -DNO_RAMFUNCS
LDFLAGS = -nostdlib -L $(LD_DIR) -T $(LD_FILE) -Map=$(@:.elf=.map) --cref
LDFLAGS += --gc-sections
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
	../EFM32HG/Source/startup_efm32hg.c

SRC_ALL = $(SRC_C) $(SRC_SL)

OBJS=$(SRC_C:.c=.o)

all: $(SRC_C)
	$(CC) -o $@  $(CFLAGS) $(SRC_C)

.PHONY: all