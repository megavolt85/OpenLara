ronindir = ../../../../libronin
SH_ELF_BIN = /opt/toolchains/dc/roninchain/bin

DEBUG = 1

OPTIMISE = -Wall -Os -fomit-frame-pointer -ffast-math
#OPTIMISE += -funroll-loops -fschedule-insns2 -fdelete-null-pointer-checks 

CC = $(SH_ELF_BIN)/sh-elf-gcc -ml -m4-single-only
CXX = $(SH_ELF_BIN)/sh-elf-g++ -ml -m4-single-only -std=gnu++11
AS = $(SH_ELF_BIN)/sh-elf-as -little

INCLUDES = $(ronindir)/include ../.. ../dc

CFLAGS = $(OPTIMISE) -D__DC__ $(addprefix -I,$(INCLUDES)) -Wno-format -Wno-unused-variable -Wno-unused-but-set-variable -Wno-unused-value -Wno-sign-compare
CXXFLAGS = $(CFLAGS) -fno-exceptions -fno-rtti -Wno-class-memaccess
LDFLAGS = -Wl,-Tshlelf.xc -nostartfiles $(ronindir)/lib/crt0.o
LIBS = -L$(ronindir)/lib -lronin -lz -lm -lstdc++

TARGET	= oldc-debug.elf

ifeq ($(DEBUG), 0)
  CFLAGS  += -DNOSERIAL
  LIBS = -L$(ronindir)/lib -lronin-noserial -lz -lm -lstdc++
  TARGET = oldc-nodebug.elf
endif


OBJS = main.o
#OBJS += ../../libs/stb_vorbis/stb_vorbis.o
OBJS +=  ../../libs/tinf/tinflate.o
OBJS += dc_hardware.o libpspvram/valloc.o vm_file.o vmu.o
OBJS += primitive/polygon.o primitive/modifier.o primitive/prim_buffer.o

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) $(LDFLAGS) $(OBJS) -o $(TARGET) $(LIBS)

clean:
	$(RM) $(OBJS) $(TARGET)

run: $(TARGET)
	$(KOS_LOADER) $(TARGET) -i TR1.iso

runpc: $(TARGET)
	$(KOS_LOADER) $(TARGET) -i TR1PC.iso

runss: $(TARGET)
	$(KOS_LOADER) $(TARGET) -i TR1SS.iso

distclean:
	rm -f *.ISO *.CDI *.BIN *.elf *.bin *.iso cd/1ST_READ.BIN

dist: 1ST_READ.BIN

1ST_READ.BIN: demo.bin
	$(KOS_BASE)/utils/scramble/scramble $< $@

demo.bin: $(TARGET)
	$(SH_ELF_BIN)/sh-elf-objcopy -S -R .stack -O binary $< $@

runbin: demo.bin
	$(KOS_LOADER) demo.bin -i TR1.iso

cdi: dist
	cp 1ST_READ.BIN cd/
	mkisofs -C 0,11702 -G cd/IP.BIN -l -o TMP.ISO cd
	./cdi4dc TMP.ISO 'OpenLara.cdi'

rel:
	p7zip 'OpenLara.cdi'

banner_data:
	echo 'static unsigned char banner_data[] = {' >> $@.tmp
	hexdump -v -e '16/1 "0x%02x, " "\n"' \
	data/banner.bin >> $@.tmp
	echo '		0 };' >> $@.tmp

icon_data_2bpp:
	echo 'static unsigned char icon_data_2bpp[] = {' >> $@.tmp
	hexdump -v -s 9 -e '16/1 "0x%02x, " "\n"' \
	icon.pbm >> $@.tmp
	echo '		0 };' >> $@.tmp
