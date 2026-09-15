KSRC ?= /usr/src/linux-source-6.12
SRC  ?= $(KSRC)/drivers/input/joystick/xpad.c

BUILD := $(CURDIR)/build
XPAD  := $(CURDIR)/xpad.c
PATCH := $(CURDIR)/patch/beitong-kp-series-ms-os-desc-linux-6.12-v2.patch

DKMS_NAME     ?= xpad-beitong
DKMS_VERSION  ?= 1.0
DKMS_SRC      := /usr/src/$(DKMS_NAME)-$(DKMS_VERSION)
DKMS_TEMPLATE := $(CURDIR)/dkms.conf.in

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

dkms-install: dkms-uninstall
	@test -f "$(XPAD)" || { \
		echo "error: $(XPAD) not found"; \
		exit 1; \
	}
	@test -f "$(DKMS_TEMPLATE)" || { \
		echo "error: $(DKMS_TEMPLATE) not found"; \
		exit 1; \
	}
	@command -v dkms >/dev/null || { \
		echo "error: dkms is not installed"; \
		exit 1; \
	}

	sudo mkdir -p "$(DKMS_SRC)"
	sudo cp "$(XPAD)" "$(DKMS_SRC)/xpad.c"
	sudo cp "$(CURDIR)/Makefile.ko" "$(DKMS_SRC)/Makefile"
	sudo cp "$(DKMS_TEMPLATE)" "$(DKMS_SRC)/dkms.conf"

	sudo sed -i \
		-e 's|@PACKAGE_NAME@|$(DKMS_NAME)|g' \
		-e 's|@PACKAGE_VERSION@|$(DKMS_VERSION)|g' \
		"$(DKMS_SRC)/dkms.conf"

	sudo dkms add -m "$(DKMS_NAME)" -v "$(DKMS_VERSION)"
	sudo dkms build -m "$(DKMS_NAME)" -v "$(DKMS_VERSION)"
	sudo dkms install -m "$(DKMS_NAME)" -v "$(DKMS_VERSION)"

dkms-uninstall:
	-sudo dkms remove -m "$(DKMS_NAME)" -v "$(DKMS_VERSION)" --all
	sudo rm -rf "$(DKMS_SRC)"

.PHONY: dkms-install dkms-uninstall
