# -*- mode: ruby -*-
# vi: set ft=ruby :

# MobileInsight Vagrant Installation Script
# Copyright (c) 2017 MobileInsight Team
# Author: Zengwen Yuan, zyuan (at) cs.ucla.edu
# Version: 1.2

$INSTALL_BASE = <<SCRIPT
apt-get update
apt-get -y install build-essential pkg-config
apt-get -y install autoconf automake zlib1g-dev libtool
apt-get -y install bison byacc flex ccache
apt-get -y install unzip
apt -y install python3.8
apt-get -y install libncurses5

apt-get -y install cmake pkg-config wget libglib2.0-dev bison flex libpcap-dev libgcrypt-dev qt5-default qttools5-dev qtmultimedia5-dev libqt5svg5-dev libc-ares-dev libsdl2-mixer-2.0-0 libsdl2-image-2.0-0 libsdl2-2.0-0

# alias python=python3
# apt-get -y install libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 libbz2-1.0:i386
# gem install android-sdk-installer
# easy_install pip

SCRIPT


$CREATE_NDK_TOOLCHAIN = <<SCRIPT
# Download and setup Android NDK r15c
cd ~
wget https://dl.google.com/android/repository/android-ndk-r15c-linux-x86_64.zip
unzip android-ndk-r15c-linux-x86_64.zip
echo 'export ANDROID_NDK_HOME=/home/vagrant/android-ndk-r15c' >> ~/.bashrc
echo 'PATH=$PATH:$ANDROID_NDK_HOME' >> ~/.bashrc
source ~/.bashrc
rm android-ndk-r15c-linux-x86_64.zip

# Create MobileInsight dev folder at /home/vagrant/mi-dev
cd ~/android-ndk-r15c
python3 build/tools/make_standalone_toolchain.py \
    --arch arm \
    --api 26 \
    --stl gnustl \
    --unified-headers \
    --install-dir /home/vagrant/android-ndk-toolchain

cd ~
cp /vagrant/envsetup.sh .
chmod +x envsetup.sh
source envsetup.sh

SCRIPT


$DOWNLOAD_TARBALLS = <<SCRIPT
wget http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.15.tar.gz
tar xf libiconv-1.15.tar.gz
rm libiconv-1.15.tar.gz

wget http://ftp.gnu.org/pub/gnu/gettext/gettext-0.19.8.tar.gz
tar xf gettext-0.19.8.tar.gz
rm gettext-0.19.8.tar.gz

# wget https://gnupg.org/ftp/gcrypt/libgpg-error/libgpg-error-1.27.tar.bz2
# tar xf libgpg-error-1.27.tar.bz2
# rm libgpg-error-1.27.tar.bz2

wget https://gnupg.org/ftp/gcrypt/libgpg-error/libgpg-error-1.37.tar.gz
tar xf libgpg-error-1.37.tar.gz
rm libgpg-error-1.37.tar.gz

wget https://gnupg.org/ftp/gcrypt/libgcrypt/libgcrypt-1.8.1.tar.bz2
tar xf libgcrypt-1.8.1.tar.bz2
rm libgcrypt-1.8.1.tar.bz2

wget http://ftp.gnome.org/pub/gnome/sources/glib/2.54/glib-2.54.3.tar.xz
tar xf glib-2.54.3.tar.xz
rm glib-2.54.3.tar.xz
# wget https://download.gnome.org/sources/glib/2.61/glib-2.61.3.tar.xz
# tar xf glib-2.61.3.tar.xz
# rm glib-2.61.3.tar.xz

ws_ver=3.4.0
wget  http://www.mobileinsight.net/wireshark-${ws_ver}-rbc-dissector.tar.xz -O wireshark-${ws_ver}.tar.xz
tar -xf wireshark-${ws_ver}.tar.xz
rm wireshark-${ws_ver}.tar.xz

SCRIPT


$COMPILE_LIBICONV = <<SCRIPT
cd ~/libiconv-1.15
./configure --build=${BUILD_SYS} --host=arm-eabi --prefix=${PREFIX} --disable-rpath
make
make install
SCRIPT


$COMPILE_GETTEXT = <<SCRIPT
cd ~/gettext-0.19.8
./configure --build=${BUILD_SYS} --host=arm-eabi  --prefix=${PREFIX} --disable-rpath --disable-java --disable-native-java --disable-libasprintf --disable-openmp --disable-curses
make
make install

SCRIPT


$COMPILE_LIBGPGERROR = <<SCRIPT
cd ~/libgpg-error-1.37
./configure --build=${BUILD_SYS} --host=${TOOLCHAIN} --prefix=${PREFIX} --enable-static --disable-shared
make
make install

SCRIPT


$COMPILE_LIBGCRYPT = <<SCRIPT
cd ~/libgcrypt-1.8.1
./configure --build=${BUILD_SYS} --host=${TOOLCHAIN} --prefix=${PREFIX} --enable-static --disable-shared
make
make install

SCRIPT


$COMPILE_PCAP = <<SCRIPT
wget http://www.tcpdump.org/release/libpcap-1.9.1.tar.gz
tar -zxvf libpcap-1.9.1.tar.gz
rm libpcap-1.9.1.tar.gz
cd ~/libpcap-1.9.1
./configure --build=${BUILD_SYS} --host=${TOOLCHAIN} --prefix=${PREFIX} --enable-static --disable-shared
make
make install
SCRIPT


$COMPILE_GLIB = <<SCRIPT

# Install libffi
cd ~
wget https://github.com/libffi/libffi/releases/download/v3.3/libffi-3.3.tar.gz
tar -xf libffi-3.3.tar.gz
rm libffi-3.3.tar.gz

cd ~/libffi-3.3
./configure --build=${BUILD_SYS} --host=${TOOLCHAIN} --prefix=${PREFIX} --enable-static --disable-shared
make
make install

cd ~
wget https://github.com/c-ares/c-ares/releases/download/cares-1_15_0/c-ares-1.15.0.tar.gz
tar -xf c-ares-1.15.0.tar.gz
rm c-ares-1.15.0.tar.gz

cd ~/c-ares-1.15.0/
unset CFLAGS
./configure --build=${BUILD_SYS} --host=${TOOLCHAIN} --prefix=${PREFIX} --enable-static --disable-shared
make
make install

# Reset the CFLAGS
cd ~
source envsetup.sh
cd ~/glib-2.54.3
# edit the android cache following http://zwyuan.github.io/2016/07/17/cross-compile-glib-for-android/
./configure --build=${BUILD_SYS} --host=${TOOLCHAIN} --prefix=${PREFIX} --disable-dependency-tracking --cache-file=android.cache --enable-included-printf --enable-static --with-pcre=no --disable-libmount
make
make install

SCRIPT


$COMPILE_WIRESHARK = <<SCRIPT
# Download ws with the patch for android
cd ~/wireshark-3.4.0
cmake \
    -DCMAKE_PREFIX_PATH=${PREFIX} \
    -DCMAKE_TOOLCHAIN_FILE=$NDK/build/cmake/android.toolchain.cmake \
    -DANDROID_ABI=armeabi-v7a \
    -DANDROID_NATIVE_API_LEVEL=26 \
    -DCMAKE_LIBRARY_PATH=${PREFIX} \
    -DGLIB2_LIBRARY=${PREFIX}/lib/libglib-2.0.so \
    -DGLIB2_MAIN_INCLUDE_DIR=${PREFIX}/include/glib-2.0 \
    -DGLIB2_INTERNAL_INCLUDE_DIR=${PREFIX}/lib/glib-2.0/include \
    -DGMODULE2_LIBRARY=${PREFIX}/lib/libgmodule-2.0.so \
    -DGMODULE2_INCLUDE_DIR=${PREFIX}/include/glib-2.0 \
    -DGTHREAD2_LIBRARY=${PREFIX}/lib/libgthread-2.0.so \
    -DGTHREAD2_INCLUDE_DIR=${PREFIX}/include/glib-2.0 \
    -DGCRYPT_LIBRARY=${PREFIX}/lib/libgcrypt.a \
    -DGCRYPT_INCLUDE_DIR=${PREFIX}/include \
    -DGCRYPT_ERROR_LIBRARY=${PREFIX}/lib/libgpg-error.a \
    -DCARES_LIBRARY=${PREFIX}/lib/libcares.a \
    -DCARES_INCLUDE_DIR=${PREFIX}/include \
    -DCMAKE_TOOLCHAIN_FILE=$NDK/build/cmake/android.toolchain.cmake \
    -DANDROID_ABI=armeabi-v7a \
    -DANDROID_NATIVE_API_LEVEL=26 \
    -DENABLE_gnutls=OFF \
    -DENABLE_plugins=OFF \
    -DENABLE_pcap=OFF \
    -DENABLE_libgcrypt-prefix=OFF \
    -DBUILD_wireshark=OFF \
    -DBUILD_packet-editor=OFF \
    -DBUILD_profile-build=OFF \
    -DBUILD_tshark=OFF \
    -DBUILD_editcap=OFF \
    -DBUILD_capinfos=OFF \
    -DBUILD_captype=OFF \
    -DBUILD_mergecap=OFF \
    -DBUILD_reordercap=OFF \
    -DBUILD_text2pcap=OFF \
    -DBUILD_dftest=OFF \
    -DBUILD_randpkt=OFF \
    -DBUILD_dumpcap=OFF \
    -DBUILD_rawshark=OFF \
    -DBUILD_sharkd=OFF \
    -DBUILD_tfshark=OFF \
    -DBUILD_pcap-ng-default=OFF \
    -DBUILD_androiddump=OFF \
    -DBUILD_sshdump=OFF \
    -DBUILD_ciscodump=OFF \
    -DBUILD_randpktdump=OFF \
    -DBUILD_udpdump=OFF .
# Replace lemon with the prebuilt one
make
make install

SCRIPT


$COPY_LIBS = <<SCRIPT
# Copy MobileInsight apk to local folder
cd ~
mkdir ws_libs
cd ws_libs
cp ~/androidcc/lib/libglib-2.0.so .
cp ~/androidcc/lib/libgobject-2.0.so .
cp ~/androidcc/lib/libgmodule-2.0.so .
cp ~/androidcc/lib/libgthread-2.0.so .
cp ~/androidcc/lib/libgcap-2.0.so .
cp /usr/local/lib/libwireshark.so .
cp /usr/local/lib/libwiretap.so .
cp /usr/local/lib/libwsutil.so .

cp -r ~/ws_libs /vagrant/

SCRIPT

Vagrant.configure(2) do |config|
#   config.vm.box = "bento/ubuntu-16.04"
#   config.vm.box_version = "201708.22.0"
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.box_version = "202004.27.0"

  config.vm.provider "virtualbox" do |vb|
    # # Display the VirtualBox GUI when booting the machine
    # vb.gui = true

    # Customize the amount of memory and cpus on the VM:
    vb.memory = "8192"
    vb.cpus = 8
  end

  config.vm.provision "shell", privileged: true, inline: $INSTALL_BASE

# It's better to do the remaining part manually.

#   config.vm.provision "shell", privileged: false, keep_color: true, inline: $CREATE_NDK_TOOLCHAIN
#   config.vm.provision "shell", privileged: false, keep_color: true, inline: $DOWNLOAD_TARBALLS
#   config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_LIBICONV
#   config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_GETTEXT
#   config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_LIBGPGERROR
#   config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_LIBGCRYPT
#   config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_PCAP
#   config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_GLIB
#   config.vm.provision "shell", privileged: false, keep_color: true, inline: $COMPILE_WIRESHARK
#   config.vm.provision "shell", privileged: false, keep_color: true, inline: $COPY_LIBS
end