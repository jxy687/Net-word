### libxmp
```
git clone https://github.com/libxmp/libxmp.git

cd libxmp

nano build_riscv.sh

```

build_riscv.sh
```
#!/bin/bash

# --- 1. 局部路径定义 ---
# 确保路径指向你存放虚拟环境的准确位置
VENV_PATH="$HOME/RuyiSDKGames/CppND-Capstone-Snake-Game-Hand/gnu-plct-venv"
SYSROOT="$VENV_PATH/sysroot"
TOOLCHAIN_BIN="$VENV_PATH/bin"

# 修正前缀：riscv64-plct-linux-gnu-
TOOLCHAIN_PREFIX="riscv64-plct-linux-gnu-"

# --- 2. 编译器绝对路径 ---
CC_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}gcc"
CXX_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}g++"

# --- 3. 配置与编译 ---
# 修复点：添加 -lm 强制链接数学库，解决 pow/log 等符号未定义问题
PKG_CONFIG_SYSROOT_DIR="$SYSROOT" \
PKG_CONFIG_LIBDIR="$SYSROOT/usr/lib64/pkgconfig:$SYSROOT/usr/share/pkgconfig" \
cmake -S . -B build \
  -DCMAKE_SYSTEM_NAME=Linux \
  -DCMAKE_SYSTEM_PROCESSOR=riscv64 \
  -DCMAKE_C_COMPILER="$CC_EXEC" \
  -DCMAKE_CXX_COMPILER="$CXX_EXEC" \
  -DCMAKE_SYSROOT="$SYSROOT" \
  -DCMAKE_FIND_ROOT_PATH="$SYSROOT" \
  -DCMAKE_INSTALL_PREFIX="/usr" \
  -DCMAKE_STAGING_PREFIX="$SYSROOT/usr" \
  -DCMAKE_EXE_LINKER_FLAGS="-lm" \
  -DCMAKE_SHARED_LINKER_FLAGS="-lm" \
  -DCMAKE_BUILD_TYPE=Release

# 执行编译
cmake --build build -j$(nproc)

# 执行安装 (安装到 SYSROOT/usr)
cmake --install build
```
```
chmod +x build_riscv.sh
```
### 使用 dnf 注入依赖到 Sysroot
```
 sudo apt install -y qemu-user-static dnf
```
```
# 定义你的虚拟环境 sysroot 路径
export MY_SYSROOT="$HOME/RuyiSDKGames/CppND-Capstone-Snake-Game-Hand/gnu-plct-venv/sysroot"

# 使用 dnf 直接安装到该目录
sudo dnf --installroot=$MY_SYSROOT \
    --forcearch=riscv64 \
    --releasever=24.03 \
    --repofrompath=oe-base,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/OS/riscv64/ \
    --repofrompath=oe-everything,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/everything/riscv64/ \
    --repofrompath=oe-epol,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/EPOL/main/riscv64/ \
    --repofrompath=oe-update,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/update/riscv64/ \
    --disablerepo=* --enablerepo=oe-base,oe-everything,oe-epol,oe-update \
    --nogpgcheck \
    --setopt=install_weak_deps=False \
    install -y fluidsynth-devel opus-devel opusfile-devel libogg-devel wavpack-devel
```
配置权限
```
sudo chown -R $USER:$USER $MY_SYSROOT
```
### SDL_mixer
```
git clone https://github.com/libsdl-org/SDL_mixer.git
cd SDL_mixer
git checkout SDL2
```
build_riscv.sh
```
#!/bin/bash

# --- 1. 局部路径定义 ---
VENV_PATH="$HOME/RuyiSDKGames/CppND-Capstone-Snake-Game-Hand/gnu-plct-venv"
SYSROOT="$VENV_PATH/sysroot"
TOOLCHAIN_BIN="$VENV_PATH/bin"
TOOLCHAIN_PREFIX="riscv64-plct-linux-gnu-"

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
  -DCMAKE_EXE_LINKER_FLAGS="-lm" \
  -DCMAKE_SHARED_LINKER_FLAGS="-lm" \
  -DCMAKE_BUILD_TYPE=Release \
  -DSDL2_INCLUDE_DIR="$SYSROOT/usr/include/SDL2"

# --- 4. 编译与安装 ---
cmake --build build -j$(nproc)
cmake --install build
```

权限
```
# 递归修改整个虚拟环境的所有权
sudo chown -R $USER:$USER ~/RuyiSDKGames/CppND-Capstone-Snake-Game-Hand/gnu-plct-venv/
# 显式增加写权限，防止某些目录被设为只读
chmod -R u+w ~/RuyiSDKGames/CppND-Capstone-Snake-Game-Hand/gnu-plct-venv/sysroot
```
```
./build_riscv.sh
```

### SDL_ttf
```
# 定义你的虚拟环境 sysroot 路径
export MY_SYSROOT="$HOME/RuyiSDKGames/CppND-Capstone-Snake-Game-Hand/gnu-plct-venv/sysroot"

# 使用 dnf 直接安装到该目录
sudo dnf --installroot=$MY_SYSROOT \
    --forcearch=riscv64 \
    --releasever=24.03 \
    --repofrompath=oe-base,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/OS/riscv64/ \
    --repofrompath=oe-everything,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/everything/riscv64/ \
    --repofrompath=oe-epol,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/EPOL/main/riscv64/ \
    --repofrompath=oe-update,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/update/riscv64/ \
    --disablerepo=* --enablerepo=oe-base,oe-everything,oe-epol,oe-update \
    --nogpgcheck \
    --setopt=install_weak_deps=False \
    install -y freetype-devel

```
```
git clone https://github.com/libsdl-org/SDL_ttf.git

cd SDL_ttf

git checkout SDL2
```
build_riscv.sh
```
#!/bin/bash

# --- 1. 局部路径定义 ---
VENV_PATH="$HOME/RuyiSDKGames/CppND-Capstone-Snake-Game-Hand/gnu-plct-venv"
SYSROOT="$VENV_PATH/sysroot"
TOOLCHAIN_BIN="$VENV_PATH/bin"
TOOLCHAIN_PREFIX="riscv64-plct-linux-gnu-"

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
  -DCMAKE_EXE_LINKER_FLAGS="-lm" \
  -DCMAKE_SHARED_LINKER_FLAGS="-lm" \
  -DCMAKE_BUILD_TYPE=Release \
  -DSDL2_INCLUDE_DIR="$SYSROOT/usr/include/SDL2"

# --- 4. 编译与安装 ---
cmake --build build -j$(nproc)
cmake --install build
```

### SDL2_image
```
# 定义你的虚拟环境 sysroot 路径
export MY_SYSROOT="$HOME/RuyiSDKGames/CppND-Capstone-Snake-Game-Hand/gnu-plct-venv/sysroot"

# 使用 dnf 直接安装到该目录
sudo dnf --installroot=$MY_SYSROOT \
    --forcearch=riscv64 \
    --releasever=24.03 \
    --repofrompath=oe-base,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/OS/riscv64/ \
    --repofrompath=oe-everything,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/everything/riscv64/ \
    --repofrompath=oe-epol,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/EPOL/main/riscv64/ \
    --repofrompath=oe-update,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/update/riscv64/ \
    --disablerepo=* --enablerepo=oe-base,oe-everything,oe-epol,oe-update \
    --nogpgcheck \
    --setopt=install_weak_deps=False \
    install -y libpng-devel libjpeg-turbo-devel libwebp-devel libtiff-devel SDL2-static
```
```
git clone https://github.com/libsdl-org/SDL_image.git

cd SDL_image

git checkout SDL2

```

build_riscv.sh
```
#!/bin/bash

# --- 1. 局部路径定义 ---
# 这里的路径根据你之前提供的虚拟环境位置设定
VENV_PATH="$HOME/RuyiSDKGames/CppND-Capstone-Snake-Game-Hand/gnu-plct-venv"
SYSROOT="$VENV_PATH/sysroot"
TOOLCHAIN_BIN="$VENV_PATH/bin"
# 修正后的工具链前缀
TOOLCHAIN_PREFIX="riscv64-plct-linux-gnu-"

# --- 2. 编译器绝对路径 ---
CC_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}gcc"
CXX_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}g++"

# --- 3. 环境清理 ---
rm -rf build
mkdir build

# --- 4. 配置与交叉编译 ---
# 整合了你提供的关闭 AVIF/TIF/JXL 的参数
# 同时强制指定 SDL2_INCLUDE_DIR 避免越权访问宿主机的 /usr/include
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
  -DSDL2IMAGE_AVIF=OFF \
  -DSDL2IMAGE_TIF=OFF \
  -DSDL2IMAGE_JXL=OFF \
  -DSDL2IMAGE_VENDORED=OFF \
  -DSDL2_INCLUDE_DIR="$SYSROOT/usr/include/SDL2"

# --- 5. 执行编译 ---
cmake --build build -j$(nproc)

# --- 6. 执行安装 ---
# 安装到 CMAKE_STAGING_PREFIX 指定的 sysroot/usr 目录下
cmake --install build
```
```
chmod +x ./build_riscv.sh 
```


