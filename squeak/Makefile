include ../Makefile.inc

UPSTREAM=http://squeakvm.org/unix/release/Squeak-4.10.2.2593-src.tar.gz
IMAGE_UPSTREAM=http://ftp.squeak.org/4.6/Squeak4.6-15102.zip
TARBALL=$(notdir $(UPSTREAM))
IMAGE=$(notdir $(IMAGE_UPSTREAM))

CFLAGS=-I$(RUMPRUN_PKGS_DIR)/include
LDFLAGS=-L$(RUMPRUN_PKGS_DIR)/lib
export CFLAGS LDFLAGS

all: bin/squeak images/examples.iso

SQUEAK_CONFIGURE_OPTS += -Wno-dev \
	-DOPT--without-FT2Plugin=1 \
	-DOPT--without-Mpeg3Plugin=1 \
	-DOPT--without-vm-display-Quartz=1 \
	-DOPT--without-Klatt=1 \
	-DOPT--without-FloatMathPlugin=1 \
	-DHAVE_DLOPEN=0

build/unix: | dl/$(TARBALL)
	mkdir -p build/bld
	(cd build && tar -zx --strip-components 1 -f ../dl/$(TARBALL))
	(cd build && ../../scripts/apply-patches.sh ./ ../patches/*)

build/bld/Makefile: build/unix
	( mkdir -p build/bld; \
		cd build/bld; \
		cmake "../unix" -DVM_HOST="x86_64-rumprun-netbsd" -DVM_VERSION="6521" -DCMAKE_TOOLCHAIN_FILE=$(RUMPRUN_CMAKE_TOOLCHAIN_FILE) $(SQUEAK_CONFIGURE_OPTS))

build/bld/squeakvm: build/bld/Makefile
	(cd build; \
		make)

bin/squeak: build/bld/squeakvm
	mkdir -p bin
	cp build/bld/squeakvm bin/sqeakvm

dl/$(TARBALL):
	../scripts/fetch.sh ${UPSTREAM} dl/$(TARBALL)

dl/$(IMAGE):
	../scripts/fetch.sh $(IMAGE_UPSTREAM) dl/$(IMAGE)

images/examples: | dl/$(IMAGE)
	mkdir -p images/examples
	unzip dl/$(IMAGE) images/examples

images/examples.iso: images/examples
	genisoimage -l -r -o $@ images/examples

.PHONY: clean
clean:
	-$(MAKE) -C build clean
	rm -f bin/*
