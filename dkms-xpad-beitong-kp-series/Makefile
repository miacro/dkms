KSRC ?= /usr/src/linux-source-6.12
SRC  ?= $(KSRC)/drivers/input/joystick/xpad.c

BUILD := $(CURDIR)/build
XPAD  := $(CURDIR)/xpad.c
PATCH := $(CURDIR)/patch/beitong-kp-series-ms-os-desc-linux-6.12-v2.patch

.PHONY: all prepare patch clean distclean

all: prepare
	$(MAKE) -C "$(BUILD)"

prepare:
	mkdir -p "$(BUILD)"
	ln -sfn ../Makefile.ko "$(BUILD)/Makefile"
	ln -sfn ../xpad.c "$(BUILD)/xpad.c"

patch:
	cp "$(SRC)" "$(XPAD)"
	mkdir -p "$(BUILD)/drivers/input/joystick"
	cp "$(XPAD)" "$(BUILD)/drivers/input/joystick/xpad.c"
	cd "$(BUILD)" && patch -p1 < "$(PATCH)"
	cp "$(BUILD)/drivers/input/joystick/xpad.c" "$(XPAD)"
	rm -rf "$(BUILD)/drivers"

clean: prepare
	$(MAKE) -C "$(BUILD)" clean

distclean:
	rm -rf "$(BUILD)"
