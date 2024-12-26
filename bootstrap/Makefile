CC:=clang
CFLAGS:=-Wall -Wextra -Wno-unused -Wno-switch
CFLAGS+=-Wno-unused-parameter -Wno-missing-field-initializers -Wno-format-zero-length
CFLAGS+=-pipe -std=c99 -O0 -g
LDFLAGS:=-ldl

SYSTEM:=$(shell uname -s)
ARCH:=$(shell uname -m)

ifeq (Linux,$(SYSTEM))
BOOTSTRAP_FILE:=bootstrap.c
endif
ifeq (Darwin,$(SYSTEM))
ifeq (arm64,$(ARCH))
BOOTSTRAP_FILE:=bootstrap-darwin-arm64.c
else
BOOTSTRAP_FILE:=bootstrap-darwin-x86_64.c
endif
endif
ifeq (FreeBSD,$(SYSTEM))
LDFLAGS:=
BOOTSTRAP_FILE:=bootstrap-freebsd-amd64.c
endif
ifeq (OpenBSD,$(SYSTEM))
LDFLAGS:=
BOOTSTRAP_FILE:=bootstrap-openbsd-amd64.c
endif

all: ../output/c2c/c2c

../output/c2c/bootstrap: $(BOOTSTRAP_FILE)
		@echo "---- compiling bootstrap compiler ----"
		@echo using $(BOOTSTRAP_FILE)
		@mkdir -p ../output/c2c
		@$(CC) $(CFLAGS) $(BOOTSTRAP_FILE) -o ../output/c2c/bootstrap $(LDFLAGS)

../output/c2c/c2c: ../output/c2c/bootstrap
		@echo "---- running (bootstrapped) c2c ----"
		@../output/c2c/bootstrap c2c --fast --noplugins
		@echo "---- running c2c (no plugins) ----"
		@../output/c2c/c2c --noplugins --fast
		@(cd .. && ./install_plugins.sh )
		@echo "---- running c2c (optimized with plugins) ----"
		@../output/c2c/c2c
		@(cd .. && ./install_plugins.sh )

bootstrap-darwin-arm64.c: bootstrap-darwin-arm64.patch bootstrap.c
		@patch -s -o $@ bootstrap.c $*.patch

bootstrap-darwin-x86_64.c: bootstrap-darwin-x86_64.patch bootstrap.c
		@patch -s -o $@ bootstrap.c $*.patch

bootstrap-freebsd-amd64.c: bootstrap-freebsd-amd64.patch bootstrap.c
		@patch -s -o $@ bootstrap.c $*.patch

bootstrap-openbsd-amd64.c: bootstrap-openbsd-amd64.patch bootstrap.c
		@patch -s -o $@ bootstrap.c $*.patch

clean:
		@rm -rf ../output/

