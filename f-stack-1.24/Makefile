# Makefile for F-Stack
CURDIR := $(shell pwd)
SDK := $(CURDIR)/SDK
SDK_DPDK := $(SDK)/dpdk
SDK_FSTACK := $(SDK)/f-stack
SDK_NGINX := $(SDK)/nginx
SDK_OPENRESTY := $(SDK)/openresty

DPDK_PATH := $(CURDIR)/dpdk
APP_PATH := $(CURDIR)/app
NGINX_PATH := $(APP_PATH)/nginx-1.25.2
OPENRESTY_PATH := $(APP_PATH)/openresty-1.25.3.2

# compile DPDK
dpdk-config:
	cd $(DPDK_PATH) && meson setup build --prefix=$(SDK_DPDK)
	cd $(DPDK_PATH) && meson configure -Dtests=false  build
dpdk-reconfigure:
	cd $(DPDK_PATH) && meson configure build
dpdk-build:
	cd $(DPDK_PATH) && meson compile -C ./build
dpdk-install:
	rm -rf $(SDK_DPDK)/*
	mkdir -p $(SDK_DPDK)
	cd $(DPDK_PATH) && meson install -C build
	sudo cp $(DPDK_PATH)/build/meson-private/libdpdk-libs.pc /usr/share/pkgconfig/
	sudo cp $(DPDK_PATH)/build/meson-private/libdpdk.pc /usr/share/pkgconfig/
dpdk-clean:
	cd $(DPDK_PATH) && rm -rf build
	cd $(DPDK_PATH) && rm -rf buildtools/__pycache__
# compile f-stack
ff-build:
	#cd lib && FF_PATH=$(SDK_FSTACK) FF_DPDK=$(SDK_DPDK) make -j $(shell nproc)
	cd lib && FF_DPDK=$(SDK_DPDK) make -j $(shell nproc)
ff-install:
	rm -rf $(SDK_FSTACK)/*
	mkdir -p $(SDK_FSTACK)
	mkdir -p $(SDK_FSTACK)/lib
	mkdir -p $(SDK_FSTACK)/include
	mkdir -p $(SDK_FSTACK)/etc
	mkdir -p $(SDK_FSTACK)/bin
	cd lib && FF_PREFIX=$(SDK_FSTACK) make install
ff-clean:
	cd lib && make clean

# compile Nginx
nginx-config:
	cd $(NGINX_PATH) && \
	FF_PATH=$(SDK_FSTACK) FF_DPDK=$(SDK_DPDK) bash ./configure \
	                                               --with-cc-opt="-I$(SDK_FSTACK)/include" \
	                                               --prefix=$(SDK_NGINX) --with-ff_module
nginx-build:
	make -j $(shell nproc) -C $(NGINX_PATH)
nginx-install:
	rm -rf $(SDK_NGINX)/*
	mkdir -p $(SDK_NGINX)
	cd $(NGINX_PATH) && make install
nginx-clean:
	cd $(NGINX_PATH) && rm -rf build objs Makefile

# compile Openresty
op-config:
	cd $(OPENRESTY_PATH) && \
	FF_PATH=$(SDK_FSTACK) FF_DPDK=$(SDK_DPDK) ./configure --ff-path=$(SDK_FSTACK) --ff-dpdk=$(SDK_DPDK) \
	                                               --with-cc-opt="-I$(SDK_FSTACK)/include" \
	                                               --prefix=$(SDK_OPENRESTY) --with-ff_module
op-build:
	make -j $(shell nproc) -C $(OPENRESTY_PATH)
op-install:
	rm -rf $(SDK_OPENRESTY)/*
	mkdir -p $(SDK_OPENRESTY)
	cd $(OPENRESTY_PATH) && make install
op-clean:
	cd $(OPENRESTY_PATH) && rm -rf build Makefile

sdk-clean:
	rm -rf $(SDK)

cleanall: dpdk-clean ff-clean nginx-clean op-clean sdk-clean

#     cd f-stack
#     # Compile DPDK
#     cd dpdk/usertools
#     ./dpdk-setup.sh # compile with x86_64-native-linuxapp-gcc

#     # Set hugepage (Linux only)
#     # single-node system
#     echo 1024 > /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages

#     # or NUMA (Linux only)
#     echo 1024 > /sys/devices/system/node/node0/hugepages/hugepages-2048kB/nr_hugepages
#     echo 1024 > /sys/devices/system/node/node1/hugepages/hugepages-2048kB/nr_hugepages

#     # Using Hugepage with the DPDK (Linux only)
#     mkdir /mnt/huge
#     mount -t hugetlbfs nodev /mnt/huge

#     # Close ASLR; it is necessary in multiple process (Linux only)
#     echo 0 > /proc/sys/kernel/randomize_va_space

#     # Install python for running DPDK python scripts
#     sudo apt install python # On ubuntu
#     #sudo pkg install python # On FreeBSD
    
#     # Offload NIC
#     # For Linux:
#     modprobe uio
#     insmod /data/f-stack/dpdk/x86_64-native-linuxapp-gcc/kmod/igb_uio.ko
#     insmod /data/f-stack/dpdk/x86_64-native-linuxapp-gcc/kmod/rte_kni.ko carrier=on # carrier=on is necessary, otherwise need to be up `veth0` via `echo 1 > /sys/class/net/veth0/carrier`
#     python dpdk-devbind.py --status
#     ifconfig eth0 down
#     python dpdk-devbind.py --bind=igb_uio eth0 # assuming that use 10GE NIC and eth0

#     # For FreeBSD:
#     # Refer DPDK FreeBSD guide to set tunables in /boot/loader.conf
#     # Below is an example used for our testing machine
#     #echo "hw.nic_uio.bdfs=\"2:0:0\"" >> /boot/loader.conf
#     #echo "hw.contigmem.num_buffers=1" >> /boot/loader.conf
#     #echo "hw.contigmem.buffer_size=1073741824" >> /boot/loader.conf
#     #kldload contigmem
#     #kldload nic_uio

#     # Install DPDK
#     cd ../x86_64-native-linuxapp-gcc
#     make install

#     # On Ubuntu, use gawk instead of the default mawk.
#     #sudo apt-get install gawk  # or execute `sudo update-alternatives --config awk` to choose gawk.

#     # Install dependencies for F-Stack
#     sudo apt install gcc make libssl-dev                            # On ubuntu
#     #sudo pkg install gcc gmake openssl pkgconf libepoll-shim       # On FreeBSD
 
