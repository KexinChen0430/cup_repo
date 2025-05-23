
# When you change the memory size below, make sure to also change it in:
#
# ./sw/sections.lds
# ./sw/start.S
# ./rtl/top.v

MEM_WORDS       = 512
MEM_BYTES       = 2048

MARCH           = rv32ic
CPU_FREQ_MHZ    = 50
CC_OPT          = -Os

OBJ_FILES       = start.o main.o lib.o jtag_uart.o

TOOLS_PREFIX    = /opt/riscv64-unknown-elf-gcc-10.1.0-2020.08.2-x86_64-linux-ubuntu14/bin

TARGET          = $(TOOLS_PREFIX)/riscv64-unknown-elf

AS              = $(TARGET)-as
ASFLAGS         = -march=$(MARCH) -mabi=ilp32
LD              = $(TARGET)-gcc
LDFLAGS         = -march=$(MARCH) -g -ggdb -mabi=ilp32 -Wl,-Tsections.lds,-Map,progmem.map -ffreestanding -nostartfiles -Wl,--no-relax -Wl,--start-group,--end-group
CC              = $(TARGET)-gcc
CFLAGS          = -march=$(MARCH) -g -ggdb -mno-div -mabi=ilp32 -ffunction-sections -fdata-sections -Wall -Wextra -pedantic -DCPU_FREQ=$(CPU_FREQ_MHZ)000000 $(CC_OPT)
OBJCOPY         = $(TARGET)-objcopy
OBJDUMP         = $(TARGET)-objdump
READELF         = $(TARGET)-readelf

GDBGUI          = gdbgui

CREATE_MIF      = ../misc/create_mif.rb

.PHONY: all clean 

all: progmem.dis progmem.bin progmem0.hex progmem0.mif

progmem.dis: progmem.elf
	$(OBJDUMP) -s -D $< > $@

progmem0.hex: progmem.bin
	$(CREATE_MIF) -f hex -d $(MEM_WORDS) -w 8 -o 0 -i 4 $< > progmem0.hex
	$(CREATE_MIF) -f hex -d $(MEM_WORDS) -w 8 -o 1 -i 4 $< > progmem1.hex
	$(CREATE_MIF) -f hex -d $(MEM_WORDS) -w 8 -o 2 -i 4 $< > progmem2.hex
	$(CREATE_MIF) -f hex -d $(MEM_WORDS) -w 8 -o 3 -i 4 $< > progmem3.hex

progmem0.mif: progmem.bin
	$(CREATE_MIF) -f mif -d $(MEM_WORDS) -w 8 -o 0 -i 4 $< > progmem0.mif
	$(CREATE_MIF) -f mif -d $(MEM_WORDS) -w 8 -o 1 -i 4 $< > progmem1.mif
	$(CREATE_MIF) -f mif -d $(MEM_WORDS) -w 8 -o 2 -i 4 $< > progmem2.mif
	$(CREATE_MIF) -f mif -d $(MEM_WORDS) -w 8 -o 3 -i 4 $< > progmem3.mif

progmem.bin: progmem.elf
	$(OBJCOPY) -O binary $< $@

progmem.elf: $(OBJ_FILES) top_defines.h sections.lds Makefile 
	$(LD) $(LDFLAGS) -o $@ $(OBJ_FILES) -lm 

main.o: top_defines.h
main.c: top_defines.h

jtag_uart:
	$(QUARTUS_ROOTDIR)/bin/nios2-terminal

clean:
	\rm -fr *.o *.hex *.elf *.dis *.bin *.coe *.map *.mif *.mem *.funcs *.globs

