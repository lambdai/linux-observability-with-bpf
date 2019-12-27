CLANG = clang

################### add #######################
LLC ?= llc
CLANG ?= clang
INC_FLAGS = -nostdinc -isystem `$(CLANG) -print-file-name=include`
EXTRA_CFLAGS ?= -O2 -emit-llvm

# In case up-to-date headers are not installed locally in /usr/include,
# # use source build.
#
################### add #######################

LINUXINCLUDE += -I

EXECABLE = monitor-exec

BPFCODE = bpf_program
KERNELSRC = ~/workspace/linux
BPFTOOLS = $(KERNELSRC)/samples/bpf
BPFLOADER = $(BPFTOOLS)/bpf_load.c

CCINCLUDE += -I$(KERNELSRC)/tools/testing/selftests/bpf

linuxhdrs ?= /usr/src/linux-headers-5.4.6-050406-generic

LINUXINCLUDE =  -I/home/vagrant/workspace/linux/tools/include
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
LIBRARY_PATH = -L/usr/local/lib64
BPFSO = -lbpf


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

bpfload: build
	clang -o $(EXECABLE) -lelf $(LOADINCLUDE) $(LIBRARY_PATH) $(BPFSO) \
        $(BPFLOADER) loader.c

$(EXECABLE): bpfload

.DEFAULT_GOAL := $(EXECABLE)
