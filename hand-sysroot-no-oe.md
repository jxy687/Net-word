手动构建sysroot，使用dnf以及手动编译
## 手动构建 sysroot

## 开始
使用dnf安装一些已经存在的依赖
```
export MY_SYSROOT="$HOME/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv/sysroot"
# 把原来虚拟环境自带的sysroot里面的内容删除
rm -rf $MY_SYSROOT/*

#--setopt=install_weak_deps=True表示安装弱依赖
sudo dnf --installroot=$MY_SYSROOT \
    --forcearch=riscv64 \
    --releasever=24.03 \
    --repofrompath=oe-base,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/OS/riscv64/ \
    --repofrompath=oe-everything,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/everything/riscv64/ \
    --repofrompath=oe-epol,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/EPOL/main/riscv64/ \
    --repofrompath=oe-update,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/update/riscv64/ \
    --disablerepo=* --enablerepo=oe-base,oe-everything,oe-epol,oe-update \
    --nogpgcheck \
    --setopt=install_weak_deps=True \
    install -y SDL2-devel bash coreutils dnf openEuler-release SDL2-devel freetype-devel libpng-devel wavpack-devel mesa-dri-drivers mesa-libGL-devel libX11-devel zlib-devel openssl-devel libXext-devel libXcursor-devel libXinerama-devel libXi-devel fluidsynth-devel opus-devel opusfile-devel libogg-devel wavpack-devel freetype-devel libpng-devel libjpeg-turbo-devel libwebp-devel libtiff-devel SDL2-static glibc-devel

sudo dnf --installroot=$MY_SYSROOT \
    --forcearch=riscv64 \
    --releasever=24.03 \
    --repofrompath=oe-base,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/OS/riscv64/ \
    --repofrompath=oe-everything,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/everything/riscv64/ \
    --repofrompath=oe-epol,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/EPOL/main/riscv64/ \
    --repofrompath=oe-update,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/update/riscv64/ \
    --disablerepo=* --enablerepo=oe-base,oe-everything,oe-epol,oe-update \
    --nogpgcheck \
    --setopt=install_weak_deps=True \
    groupinstall -y "Development Tools"
```
手动安装不存在的依赖

### libxmp
build_riscv.sh
```
#!/bin/bash
# --- 1. 局部路径定义 ---
VENV_PATH="$HOME/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv"
SYSROOT="$VENV_PATH/sysroot"
TOOLCHAIN_BIN="$VENV_PATH/bin"
TOOLCHAIN_PREFIX="riscv64-plct-linux-gnu-"

export SYSROOT="$VENV_PATH/sysroot"
export QEMU_LD_PREFIX="$SYSROOT"

# --- 2. 编译器绝对路径 ---
CC_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}gcc"
CXX_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}g++"

# --- 3. 彻底清理并重新配置 ---
# 必须先删除旧目录，确保 Parse error 不会残留在缓存中
rm -rf build
mkdir build

# 关键：确保 -D 后面的参数紧凑，没有空格
PKG_CONFIG_SYSROOT_DIR="$SYSROOT" \
PKG_CONFIG_LIBDIR="$SYSROOT/usr/lib64/pkgconfig:$SYSROOT/usr/share/pkgconfig" \
PKG_CONFIG_PATH="" \
cmake -S . -B build \
  -DCMAKE_SYSTEM_NAME=Linux \
  -DCMAKE_SYSTEM_PROCESSOR=riscv64 \
  -DCMAKE_C_COMPILER="$CC_EXEC" \
  -DCMAKE_CXX_COMPILER="$CXX_EXEC" \
  -DCMAKE_SYSROOT="$SYSROOT" \
  -DCMAKE_FIND_ROOT_PATH="$SYSROOT" \
  -DCMAKE_FIND_ROOT_PATH_MODE_PROGRAM=NEVER \
  -DCMAKE_FIND_ROOT_PATH_MODE_LIBRARY=ONLY \
  -DCMAKE_FIND_ROOT_PATH_MODE_INCLUDE=ONLY \
  -DCMAKE_FIND_ROOT_PATH_MODE_PACKAGE=ONLY \
  -DCMAKE_INSTALL_PREFIX="/usr" \
  -DCMAKE_STAGING_PREFIX="$SYSROOT/usr" \
  -DCMAKE_INSTALL_LIBDIR=lib64 \
  -DCMAKE_BUILD_TYPE=Release

# --- 4. 编译与安装 ---
cmake --build build -j$(nproc)
cmake --install build
```
如果重复执行bash,会显示：up to date
```
-- Up-to-date: /home/jxy/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/sysroot/usr/lib64/libxmp.so.4
```


错误（可能出现）
<img width="1858" height="342" alt="image" src="https://github.com/user-attachments/assets/0b3d09ee-8d91-44dd-a8ba-e0b9c7568446" />
原因：这次的错误 确实是权限问题，而且原因基本可以确定：之前用 sudo dnf 往 sysroot 里安装过包，导致 sysroot 里的文件属于 root 用户。
解决办法,注意sysroot是软链接
```
sudo chown -R $USER:$USER ~/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv/sysroot

sudo chown -R $USER:$USER ~/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv/sysroot.riscv64-plct-linux-gnu

#还是不行，执行
sudo chmod -R 775 ~/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv/sysroot

sudo chmod -R 775 ~/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv/sysroot.riscv64-plct-linux-gnu
```
### SDL_mixer
build_riscv.sh
```
#!/bin/bash
# --- 1. 局部路径定义 ---
VENV_PATH="$HOME/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv"
SYSROOT="$VENV_PATH/sysroot"
TOOLCHAIN_BIN="$VENV_PATH/bin"
TOOLCHAIN_PREFIX="riscv64-plct-linux-gnu-"

export SYSROOT="$VENV_PATH/sysroot"
export QEMU_LD_PREFIX="$SYSROOT"

# --- 2. 编译器绝对路径 ---
CC_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}gcc"
CXX_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}g++"

# --- 3. 彻底清理并重新配置 ---
# 必须先删除旧目录，确保 Parse error 不会残留在缓存中
rm -rf build
mkdir build

# 关键：确保 -D 后面的参数紧凑，没有空格
PKG_CONFIG_SYSROOT_DIR="$SYSROOT" \
PKG_CONFIG_LIBDIR="$SYSROOT/usr/lib64/pkgconfig:$SYSROOT/usr/share/pkgconfig" \
PKG_CONFIG_PATH="" \
cmake -S . -B build \
  -DCMAKE_SYSTEM_NAME=Linux \
  -DCMAKE_SYSTEM_PROCESSOR=riscv64 \
  -DCMAKE_C_COMPILER="$CC_EXEC" \
  -DCMAKE_CXX_COMPILER="$CXX_EXEC" \
  -DCMAKE_SYSROOT="$SYSROOT" \
  -DCMAKE_FIND_ROOT_PATH="$SYSROOT" \
  -DCMAKE_FIND_ROOT_PATH_MODE_PROGRAM=NEVER \
  -DCMAKE_FIND_ROOT_PATH_MODE_LIBRARY=ONLY \
  -DCMAKE_FIND_ROOT_PATH_MODE_INCLUDE=ONLY \
  -DCMAKE_FIND_ROOT_PATH_MODE_PACKAGE=ONLY \
  -DCMAKE_INSTALL_PREFIX="/usr" \
  -DCMAKE_STAGING_PREFIX="$SYSROOT/usr" \
  -DCMAKE_INSTALL_LIBDIR=lib64 \
  -DCMAKE_BUILD_TYPE=Release \
  -DSDL2_INCLUDE_DIR="$SYSROOT/usr/include/SDL2"

# --- 4. 编译与安装 ---
cmake --build build -j$(nproc)
cmake --install build
```
### SDL_ttf
build_riscv.sh
```
#!/bin/bash

# --- 1. 局部路径定义 ---
VENV_PATH="$HOME/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv"
SYSROOT="$VENV_PATH/sysroot"
TOOLCHAIN_BIN="$VENV_PATH/bin"
TOOLCHAIN_PREFIX="riscv64-plct-linux-gnu-"

export SYSROOT="$VENV_PATH/sysroot"
export QEMU_LD_PREFIX="$SYSROOT"

# --- 2. 编译器绝对路径 ---
CC_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}gcc"
CXX_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}g++"

# --- 3. 彻底清理并重新配置 ---
# 必须先删除旧目录，确保 Parse error 不会残留在缓存中
rm -rf build
mkdir build

# 关键：确保 -D 后面的参数紧凑，没有空格
PKG_CONFIG_SYSROOT_DIR="$SYSROOT" \
PKG_CONFIG_LIBDIR="$SYSROOT/usr/lib64/pkgconfig:$SYSROOT/usr/share/pkgconfig" \
PKG_CONFIG_PATH="" \
cmake -S . -B build \
  -DCMAKE_SYSTEM_NAME=Linux \
  -DCMAKE_SYSTEM_PROCESSOR=riscv64 \
  -DCMAKE_C_COMPILER="$CC_EXEC" \
  -DCMAKE_CXX_COMPILER="$CXX_EXEC" \
  -DCMAKE_SYSROOT="$SYSROOT" \
  -DCMAKE_FIND_ROOT_PATH="$SYSROOT" \
  -DCMAKE_FIND_ROOT_PATH_MODE_PROGRAM=NEVER \
  -DCMAKE_FIND_ROOT_PATH_MODE_LIBRARY=ONLY \
  -DCMAKE_FIND_ROOT_PATH_MODE_INCLUDE=ONLY \
  -DCMAKE_FIND_ROOT_PATH_MODE_PACKAGE=ONLY \
  -DCMAKE_INSTALL_PREFIX="/usr" \
  -DCMAKE_STAGING_PREFIX="$SYSROOT/usr" \
  -DCMAKE_BUILD_TYPE=Release \
  -DSDL2_INCLUDE_DIR="$SYSROOT/usr/include/SDL2"

# --- 4. 编译与安装 ---
cmake --build build -j$(nproc)
cmake --install build
```
### SDL_image
build_riscv.sh
```
#!/bin/bash

# --- 1. 局部路径定义 ---
VENV_PATH="$HOME/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv"
SYSROOT="$VENV_PATH/sysroot"
TOOLCHAIN_BIN="$VENV_PATH/bin"
TOOLCHAIN_PREFIX="riscv64-plct-linux-gnu-"

export SYSROOT="$VENV_PATH/sysroot"
export QEMU_LD_PREFIX="$SYSROOT"

# --- 2. 编译器绝对路径 ---
CC_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}gcc"
CXX_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}g++"

# --- 3. 彻底清理并重新配置 ---
# 必须先删除旧目录，确保 Parse error 不会残留在缓存中
rm -rf build
mkdir build

# 关键：确保 -D 后面的参数紧凑，没有空格
PKG_CONFIG_SYSROOT_DIR="$SYSROOT" \
PKG_CONFIG_LIBDIR="$SYSROOT/usr/lib64/pkgconfig:$SYSROOT/usr/share/pkgconfig" \
PKG_CONFIG_PATH="" \
cmake -S . -B build \
  -DCMAKE_SYSTEM_NAME=Linux \
  -DCMAKE_SYSTEM_PROCESSOR=riscv64 \
  -DCMAKE_C_COMPILER="$CC_EXEC" \
  -DCMAKE_CXX_COMPILER="$CXX_EXEC" \
  -DCMAKE_SYSROOT="$SYSROOT" \
  -DCMAKE_FIND_ROOT_PATH="$SYSROOT" \
  -DCMAKE_FIND_ROOT_PATH_MODE_PROGRAM=NEVER \
  -DCMAKE_FIND_ROOT_PATH_MODE_LIBRARY=ONLY \
  -DCMAKE_FIND_ROOT_PATH_MODE_INCLUDE=ONLY \
  -DCMAKE_FIND_ROOT_PATH_MODE_PACKAGE=ONLY \
  -DCMAKE_INSTALL_PREFIX="/usr" \
  -DCMAKE_STAGING_PREFIX="$SYSROOT/usr" \
  -DCMAKE_BUILD_TYPE=Release \
  -DSDL2_INCLUDE_DIR="$SYSROOT/usr/include/SDL2"

# --- 4. 编译与安装 ---
cmake --build build -j$(nproc)
cmake --install build
```
注意输出：
```
-- - enabled:  stb bmp gif jpg lbm pcx png pnm qoi svg tga tif webp xcf xpm xv
-- - disabled: imageio wic avif jxl
```
### 编译项目
```
cmake -DCMAKE_TOOLCHAIN_FILE=$HOME/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv/toolchain.cmake \
      -DCMAKE_SYSROOT=$HOME/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv/sysroot \
      -DCMAKE_EXE_LINKER_FLAGS="-static-libstdc++ -static-libgcc" \
      ..
```
<img width="1854" height="675" alt="image" src="https://github.com/user-attachments/assets/0433b3b6-c2ab-4a9d-8f5b-2a1fcecc3b29" />

avif，tif改为OFF
<img width="1804" height="351" alt="image" src="https://github.com/user-attachments/assets/97fd5355-2114-4846-a4db-81b65fbdc73f" />
执行：
```
find usr/lib64/cmake/SDL2 -name "*.cmake" -exec sed -i 's|"/usr|"${CMAKE_SYSROOT}/usr|g' {} +
```

### 运行项目
```
cd ..

env SDL_AUDIODRIVER=dummy LIBGL_ALWAYS_SOFTWARE=1 ruyi-qemu -L ~/RuyiSDKGames/SDLShooter-Hand-3/gnu-plct-venv/sysroot/ ./build/SDLShooter-Linux
```
