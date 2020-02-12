################### add #######################
LLC ?= llc
CLANG ?= clang
INC_FLAGS = -nostdinc -isystem `$(CLANG) -print-file-name=include`
EXTRA_CFLAGS ?= -O2 -emit-llvm
################### add #######################

LINUXINCLUDE += -I

EXECABLE = monitor-exec

BPFCODE = bpf_program
KERNELSRC = $(HOME)/workspace/linux-source
BPFTOOLS = $(KERNELSRC)/samples/bpf
BPFLOADER = $(BPFTOOLS)/bpf_load.c

CCINCLUDE += -I$(KERNELSRC)/tools/testing/selftests/bpf

linuxhdrs ?= /usr/src/linux-headers-`uname -r`

LINUXINCLUDE =  -I$(KERNELSRC)/tools/include
LINUXINCLUDE += -I$(linuxhdrs)/arch/x86/include/uapi \
                -I$(linuxhdrs)/arch/x86/include/generated/uapi \
                -I$(linuxhdrs)/arch/x86/include \
                -I$(linuxhdrs)/include/generated/uapi \
                -I$(linuxhdrs)/include/uapi \
                -I$(linuxhdrs)/include


LOADINCLUDE += -I$(KERNELSRC)/samples/bpf
LOADINCLUDE += -I$(KERNELSRC)/tools/lib
LOADINCLUDE += -I$(KERNELSRC)/tools/perf
LOADINCLUDE += -I$(KERNELSRC)/tools/include
LIBRARY_PATH = -L/usr/local/lib
LIB_BPF = /usr/local/lib/libbpf.a
#LDFLAGS += $(PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib64/pkgconfig pkg-config --variable=libdir libbpf)/libbpf.a


EXTRADEF += -DHAVE_ATTR_TEST=0


INC_FLAGS = -nostdinc -isystem `$(CLANG) -print-file-name=include`

#$(OBJS):  %.o:%.c
mybuild: ${BPFCODE.c}
	$(CLANG) $(INC_FLAGS) \
		-D__KERNEL__ -D__ASM_SYSREG_H \
		-Wno-unused-value -Wno-pointer-sign \
		-Wno-compare-distinct-pointer-types \
		-Wno-gnu-variable-sized-type-not-at-end \
		-Wno-address-of-packed-member -Wno-tautological-compare \
		-Wno-unknown-warning-option \
		-I../include $(LINUXINCLUDE) \
		$(EXTRA_CFLAGS) -c $(BPFCODE:=.c) -o -| $(LLC) -march=bpf -filetype=obj -o $(BPFCODE:=.o)

.PHONY: clean $(CLANG) bpfload build mybuild

clean:
	rm -f *.o *.so $(EXECABLE)

build: ${BPFCODE.c} ${BPFLOADER}
	$(CLANG) -O2 -target bpf -c $(BPFCODE:=.c) $(LINUXINCLUDE) $(CCINCLUDE)  -o ${BPFCODE:=.o}

loader: ${BPFLOADER}
	$(CLANG) $(INC_FLAGS) \
		-D__KERNEL__ -D__ASM_SYSREG_H \
		-Wno-unused-value -Wno-pointer-sign \
		-Wno-compare-distinct-pointer-types \
		-Wno-gnu-variable-sized-type-not-at-end \
		-Wno-address-of-packed-member -Wno-tautological-compare \
		-Wno-unknown-warning-option \
		-I../include $(LINUXINCLUDE) \
		$(EXTRA_CFLAGS) -c $(BPFCODE:=.c) -o -| $(LLC) -march=bpf -filetype=obj -o $(BPFCODE:=.o)

bpfload: mybuild
	clang $(EXTRADEF) -o $(EXECABLE) -lelf $(LOADINCLUDE) $(LIBRARY_PATH) $(LIB_BPF) \
        $(BPFLOADER) loader.c

myload: mybuild
	clang $(EXTRADEF) -o $(EXECABLE) -lelf $(LOADINCLUDE) $(LIBRARY_PATH) $(LDFLAGS) \
		-I../include $(LINUXINCLUDE) \
       	$(BPFLOADER) $(LIB_BPF) loader.c		

$(EXECABLE): bpfload

.DEFAULT_GOAL := $(EXECABLE)
