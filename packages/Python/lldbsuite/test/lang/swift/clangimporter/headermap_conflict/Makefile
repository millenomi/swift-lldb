LEVEL = ../../../../make
SRCDIR := $(shell dirname $(realpath $(lastword $(MAKEFILE_LIST))))
SWIFT_OBJC_INTEROP := 1

# This test builds a .dylib whose .swiftmodule imports a Clang module
# which uses a header file "Foo.h". The main swift program also
# imports a Clang module that imports a header file called "Foo.h",
# but it is in a different directory. We are using a headermap to
# switch between the two versions of "Foo.h" during build time.

all: libDylib.dylib a.out

include $(LEVEL)/Makefile.rules

a.out: main.swift
	$(SWIFTC) -g -Onone $^ -lDylib -L$(shell pwd) -o $@ -Xcc -I$(SRCDIR) $(SWIFTFLAGS)
ifneq "$(CODESIGN)" ""
	$(CODESIGN) -s - "$@"
endif

libDylib.dylib: dylib.swift
	$(SWIFTC) -g -Onone $^ -emit-library -module-name Dylib -emit-module -Xlinker -install_name -Xlinker @executable_path/$@ -Xcc -I$(SRCDIR)/foo.hmap  -Xcc -I$(SRCDIR) $(SWIFTFLAGS)
ifneq "$(CODESIGN)" ""
	$(CODESIGN) -s - "$@"
endif

clean::
	rm -rf *.swiftmodule *.swiftdoc *.dSYM *~ lib*.dylib a.out *.o

SWIFTFLAGS += -Xcc -I$(SRCDIR)/Bar -I.
