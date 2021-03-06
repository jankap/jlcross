NPROCS:=1
OS:=$(shell uname -s)

ifeq ($(OS),Linux)
  NPROCS:=$(shell grep -c ^processor /proc/cpuinfo)
endif
ifeq ($(OS),Darwin) # Assume Mac OS X
  NPROCS:=$(shell getconf _NPROCESSORS_ONLN)
endif

OWNER=JuliaLang
REPOSITORY=julia

all: build

clone:
ifeq ($(wildcard ./$(REPOSITORY)/.),)
	git clone --depth=1 https://github.com/$(OWNER)/$(REPOSITORY).git
else
	@echo "already exists"
endif

build: clone
	echo "prefix=$(HOME)/julia_dev" > Make.user
	echo "USE_BINARYBUILDER=0" >> Make.user
	echo "MARCH=native" >> Make.user
	mv Make.user ./julia/Make.user
	make -C julia -j $(NPROCS) 1>build_log.txt 2>build_error.txt

# do it in advance
# source /opt/intel/bin/compilervars.sh -arch intel64 -platform mac 
build_mkl: clone
	echo "prefix=$(HOME)/julia_dev" > Make.user
	echo "USEICC = 0" >> Make.user
	echo "USEIFC = 0" >> Make.user
	echo "USE_INTEL_MKL = 1" >> Make.user
	echo "USE_INTEL_LIBM = 0" >> Make.user
	echo "USE_BINARYBUILDER = 0" >> Make.user
	$(shell source /opt/intel/bin/compilervars.sh intel64)
	mv Make.user ./julia/Make.user
	make -C julia -j $(NPROCS) 1>build_log.txt 2>build_error.txt

install:
	make -C julia install 1>install_log.txt 2>install_error.txt

clean:
	rm -f build_log.txt
	rm -f build_error.txt
	rm -f install_log.txt
	rm -f install_error.txt
ifneq ($(wildcard ./$(REPOSITORY)/.),)
	make -C julia clean
endif

reset:
	rm -f build_log.txt
	rm -f build_error.txt
	rm -f install_log.txt
	rm -f install_error.txt
	sudo rm -rf julia

