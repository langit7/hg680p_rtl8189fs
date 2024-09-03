# hg680p_rtl8189fs
Compile module driver rtl8189fs
```shell
# Step 1: Update to the latest kernel
# Due to incomplete header files in earlier versions, it is necessary to update to the latest kernel version.
# The requirement for each kernel version is not lower than 5.4.280, 5.10.222, 5.15.163, 6.1.100, 6.6.41.
armbian-sync
armbian-update -k 6.6
# Step 2: Install compilation tools
mkdir -p /usr/local/toolchain
cd /usr/local/toolchain
# Download the compilation tools
wget https://github.com/ophub/kernel/releases/download/dev/arm-gnu-toolchain-13.3.rel1-aarch64-aarch64-none-elf.tar.xz
# Extract
tar -Jxf arm-gnu-toolchain-13.3.rel1-aarch64-aarch64-none-elf.tar.xz

# Step 3: Download and compile the driver
# Download driver source code
cd ~/
git clone -b rtl8189fs https://github.com/jwrdegoede/rtl8189ES_linux
cd rtl8189ES_linux
# Set up the compilation environment
gun_file="arm-gnu-toolchain-13.3.rel1-aarch64-aarch64-none-elf.tar.xz"
toolchain_path="/usr/local/toolchain"
toolchain_name="gcc"
export CROSS_COMPILE="${toolchain_path}/${gun_file//.tar.xz/}/bin/aarch64-none-elf-"
export CC="${CROSS_COMPILE}gcc"
export LD="${CROSS_COMPILE}ld.bfd"
export ARCH="arm64"
export KSRC=/usr/lib/modules/$(uname -r)/build
# Set the M variable according to the actual path of the source code
export M="/root/rtl8189ES_linux"
# Compile the driver
make


# Step 4: Install the driver
sudo cp -f 8189fs.ko /lib/modules/$(uname -r)/kernel/drivers/net/wireless/
# Update module dependencies
sudo depmod -a
# Load the driver module
sudo modprobe 8189fs
# You should see the driver successfully loaded
lsmod | grep 8189fs
8189fs               1634304  0
cfg80211              937984  1 8189fs

```
