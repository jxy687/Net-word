### 手动制作 sysroot
创建虚拟环境
```
ruyi venv -t gnu-plct -e qemu-user-riscv-upstream generic ./gnu-plct-venv

rm -rf $PWD/gnu-plct-venv/sysroot/*
```

Ubuntu 上安装必要依赖并初始化 sysroot
```
sudo apt install -y qemu-user-static dnf

mkdir -p $PWD/gnu-plct-venv/sysroot/usr/bin
cp /usr/bin/qemu-riscv64-static $PWD/gnu-plct-venv/sysroot/usr/bin/

sudo dnf --installroot=$PWD/gnu-plct-venv/sysroot \
           --forcearch=riscv64 \
           --releasever=24.03 \
            --repofrompath=oe-base,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/OS/riscv64/ \
            --repofrompath=oe-update,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/update/riscv64/ \
            --disablerepo=* --enablerepo=oe-base,oe-update \
            --nogpgcheck \
            --setopt=install_weak_deps=False \
            install -y bash coreutils dnf openEuler-release

sudo cp /etc/resolv.conf $PWD/gnu-plct-venv/sysroot/etc/resolv.conf
```
调整sysroot权限
```
sudo chown -R $USER:$USER $PWD/gnu-plct-venv/sysroot && sudo chown -R $USER:$USER $PWD/gnu-plct-venv/sysroot.riscv64-plct-linux-gnu
sudo chmod -R 775 $PWD/gnu-plct-venv/sysroot && sudo chmod -R 775 $PWD/gnu-plct-venv/sysroot.riscv64-plct-linux-gnu
```

进入 openEuler 安装依赖
```
sudo chroot $PWD/gnu-plct-venv/sysroot /bin/bash

dnf install SDL2-devel freetype-devel libpng-devel wavpack-devel mesa-dri-drivers mesa-libGL-devel libX11-devel zlib-devel openssl-devel libXext-devel libXcursor-devel libXinerama-devel libXi-devel fluidsynth-devel opus-devel opusfile-devel libogg-devel wavpack-devel freetype-devel libpng-devel libjpeg-turbo-devel libwebp-devel libtiff-devel SDL2-static glibc-devel

dnf groupinstall -y "Development Tools"
```
手动编译一些依赖,以 libxmp 为例，其余还有（SDL2_mixer，SDL2_ttf，SDL2_image）
```
mknod -m 666 /dev/urandom c 1 9
mknod -m 666 /dev/random c 1 8
```
```
mkdir PkgDownload && cd PkgDownload
# 克隆 libxmp 源码
git clone https://github.com/libxmp/libxmp.git && cd libxmp
git clone https://github.com/libsdl-org/SDL_mixer.git && cd SDL_mixer && git checkout SDL2
git clone https://github.com/libsdl-org/SDL_ttf.git && cd SDL_ttf && git checkout SDL2
git clone https://github.com/libsdl-org/SDL_image.git && cd SDL_image && git checkout SDL2

## 一行
cmake -S . -B build -DCMAKE_INSTALL_PREFIX=/usr && cmake --build build -j$(nproc) && cmake --install build

## 或者
mkdir build && cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr
make -j$(nproc)
make install
ldconfig
```
完成后
```
exit
```
调整权限
```
sudo chown -R $USER:$USER $PWD/gnu-plct-venv/sysroot && sudo chown -R $USER:$USER $PWD/gnu-plct-venv/sysroot.riscv64-plct-linux-gnu
sudo chmod -R 775 $PWD/gnu-plct-venv/sysroot && sudo chmod -R 775 $PWD/gnu-plct-venv/sysroot.riscv64-plct-linux-gnu
```


建立软链接(这里都在lib64里面，不需要调整)
```
```
### 更改配置文件
```
cd $PWD/gnu-plct-venv/sysroot
#更改SDL2.config.cmake
find usr/lib64/cmake -name "*.cmake" -exec sed -i 's|"/usr|"${CMAKE_SYSROOT}/usr|g' {} +
```
<img width="439" height="498" alt="image" src="https://github.com/user-attachments/assets/6da55dab-b036-47ba-aa76-7adbc3ff221b" />

### 编译项目
```
mkdir build && cd build
# 先回到上一级
cd ..
# 直接构建
cmake -S . -B build \
      -DCMAKE_TOOLCHAIN_FILE=$PWD/gnu-plct-venv/toolchain.cmake \
      -DCMAKE_SYSROOT=$PWD/gnu-plct-venv/sysroot \
      -DCMAKE_EXE_LINKER_FLAGS="-static-libstdc++ -static-libgcc"

cd ./build && make
```
### 运行项目
激活虚拟环境
```
source ./gnu-plct-venv/bin/ruyi-activate
```
```
cd ..

env SDL_AUDIODRIVER=dummy LIBGL_ALWAYS_SOFTWARE=1 ruyi-qemu -L $PWD/gnu-plct-venv/sysroot ./build/SDLShooter-Linux
```
