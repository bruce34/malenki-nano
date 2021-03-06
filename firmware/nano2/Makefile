
# We require the avr-gcc toolchain
#
OBJDIR=obj

OBJ_FILES=main.o diag.o a7105_spi.o spi_asm.o radio.o motors.o nvconfig.o mixing.o weapons.o \
	vsense.o sticks.o
OBJECTS=$(addprefix $(OBJDIR)/,$(OBJ_FILES))
HEADERS=radio.h nvconfig.h state.h motors.h diag.h a7105_spi.h mixing.h weapons.h vsense.h\
	sticks.h

MCU=attiny3217

ELF=$(OBJDIR)/main.elf
HEX=$(OBJDIR)/main.hex

LINKFLAGS=-Os

link: $(OBJECTS) $(HEADERS)
	avr-gcc -mmcu=$(MCU) -o $(ELF) $(OBJECTS) $(LINKFLAGS)
	# see how big it is
	avr-size $(ELF)
	# disassemble, so can see what the compiler did
	avr-objdump -S $(ELF) > $(OBJDIR)/main.asm
	avr-objdump -t $(ELF) > $(OBJDIR)/main.sym
	avr-objdump -j .bss -j .data -t $(ELF) | grep 00 |sort > $(OBJDIR)/ram.txt # show ram usage
	avr-objcopy -j .text -j .data -j .rodata -O ihex $(ELF) $(HEX)
	avr-objcopy -j .text -j .data -j .rodata -O binary $(ELF) obj/main.bin

CFLAGS=-mmcu=$(MCU) -Os -Wall
$(OBJDIR)/%.o: %.c Makefile
	@mkdir -p $(OBJDIR)
	avr-gcc -c $(CFLAGS) -o $@ $<

# Assembly language compile
$(OBJDIR)/%.o: %.S Makefile $(HEADERS)
	@mkdir -p $(OBJDIR)
	avr-gcc -c $(CFLAGS) -Xassembler -a=$(OBJDIR)/$(<).lst -o $@ $<

install: link
	pyupdi.py -d tiny3217 -c /dev/ttyUSB0 -f $(HEX)
install1614: link
	pyupdi.py -d tiny1614 -c /dev/ttyUSB0 -f $(HEX)
install1617: link
	pyupdi.py -d tiny1617 -c /dev/ttyUSB0 -f $(HEX)



clean:
	rm -r obj/
