KDIR ?= /lib/modules/$(shell uname -r)/build
KSRC ?= /usr/src/linux-source-6.12
SRC  ?= $(KSRC)/drivers/input/joystick/xpad.c

BUILD := $(CURDIR)/build
PATCH := $(CURDIR)/patch/beitong-kp-series-ms-os-desc-linux-6.12-v2.patch

.PHONY: all prepare patch module clean distclean

all: module

prepare:
	mkdir -p "$(BUILD)"
	ln -sfn ../Makefile "$(BUILD)/Makefile"
	cp "$(SRC)" "$(BUILD)/xpad.c"
	mkdir -p "$(BUILD)/drivers/input/joystick"
	cp "$(BUILD)/xpad.c" "$(BUILD)/drivers/input/joystick/xpad.c"

patch: prepare
	cd "$(BUILD)" && patch -p1 < "$(PATCH)"
	cp "$(BUILD)/drivers/input/joystick/xpad.c" "$(BUILD)/xpad.c"
	rm -rf "$(BUILD)/drivers"

module: patch
	$(MAKE) -C "$(KDIR)" M="$(BUILD)" modules

clean:
	@if [ -d "$(BUILD)" ] && [ -e "$(BUILD)/Makefile" ]; then \
		$(MAKE) -C "$(KDIR)" M="$(BUILD)" clean || true; \
	fi
	rm -rf "$(BUILD)"

distclean: clean
