### 参照
https://github.com/qingwan12138/PLCT-WORK/blob/main/February/blog/ruyisdk%E4%BA%A4%E5%8F%89%E7%BC%96%E8%AF%91teeworlds.md

### 还要进入openEuler 然后安装
```
# 安装核心开发包（这是解决 crt1.o 报错的关键）
dnf install -y glibc-devel

# 为了保险起见，建议把基础开发包组都装上
dnf groupinstall -y "Development Tools"
```
### 进入 openEuler 手动安装依赖

## 手动构建 sysroot

## 开始
使用dnf安装一些已经存在的依赖
```
export MY_SYSROOT="$HOME/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/sysroot"
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
```#!/bin/bash
# --- 1. 局部路径定义 ---

# 确保路径指向你存放虚拟环境的准确位置
VENV_PATH="$HOME/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv"
SYSROOT="$VENV_PATH/sysroot"
TOOLCHAIN_BIN="$VENV_PATH/bin"
# 修正前缀：riscv64-plct-linux-gnu-
TOOLCHAIN_PREFIX="riscv64-plct-linux-gnu-"

# --- 2. 编译器绝对路径 ---
CC_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}gcc"
CXX_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}g++"

# --- 3. 配置与编译 ---
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
  -DCMAKE_BUILD_TYPE=Release

# 执行编译
cmake --build build -j$(nproc)

# 执行安装 (安装到 SYSROOT/usr)
cmake --install build
```
错误
<img width="1856" height="864" alt="image" src="https://github.com/user-attachments/assets/a191b406-57d8-4639-8857-ecc0f5781a57" />

修改后的build_riscv.sh
```
#!/bin/bash
# --- 1. 局部路径定义 ---

# 确保路径指向你存放虚拟环境的准确位置
VENV_PATH="$HOME/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv"
SYSROOT="$VENV_PATH/sysroot"
TOOLCHAIN_BIN="$VENV_PATH/bin"

export SYSROOT="$VENV_PATH/sysroot"
export QEMU_LD_PREFIX="$SYSROOT"

# 修正前缀：riscv64-plct-linux-gnu-
TOOLCHAIN_PREFIX="riscv64-plct-linux-gnu-"

# --- 2. 编译器绝对路径 ---
CC_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}gcc"
CXX_EXEC="$TOOLCHAIN_BIN/${TOOLCHAIN_PREFIX}g++"

# --- 3. 配置与编译 ---
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
  -DCMAKE_BUILD_TYPE=Release

# 执行编译
cmake --build build -j$(nproc)

# 执行安装 (安装到 SYSROOT/usr)
cmake --install build
```
错误（可能出现）
<img width="1858" height="342" alt="image" src="https://github.com/user-attachments/assets/0b3d09ee-8d91-44dd-a8ba-e0b9c7568446" />
原因：这次的错误 确实是权限问题，而且原因基本可以确定：之前用 sudo dnf 往 sysroot 里安装过包，导致 sysroot 里的文件属于 root 用户。
解决办法,注意sysroot是软链接
```
sudo chown -R $USER:$USER ~/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/sysroot

sudo chown -R $USER:$USER ~/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/sysroot.riscv64-plct-linux-gnu

#还是不行，执行
sudo chmod -R 775 ~/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/sysroot

sudo chmod -R 775 ~/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/sysroot.riscv64-plct-linux-gnu
```
### SDL_mixer
build_riscv.sh
```
#!/bin/bash

# --- 1. 局部路径定义 ---
VENV_PATH="$HOME/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv"
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
### SDL_ttf
build_riscv.sh
```
#!/bin/bash

# --- 1. 局部路径定义 ---
VENV_PATH="$HOME/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv"
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
VENV_PATH="$HOME/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv"
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
cmake -DCMAKE_TOOLCHAIN_FILE=$HOME/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/toolchain.cmake \
      -DCMAKE_SYSROOT=$HOME/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/sysroot \
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

env SDL_AUDIODRIVER=dummy LIBGL_ALWAYS_SOFTWARE=1 ruyi-qemu -L ~/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/sysroot/ ./build/SDLShooter-Linux
```
<img width="1859" height="75" alt="image" src="https://github.com/user-attachments/assets/ac91835e-1c9e-4ca7-80a1-2351bc426bf0" />

如果你的程序（SDLShooter-Linux）是 64 位的，它可能会首选去 usr/lib64 目录下找库。由于报错的 sysroot 里库在 usr/lib，你可以手动创建一个软链接：

```
cd ~/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/sysroot/usr/
ln -s lib lib64
```
```
# 进入库文件实际存在的目录
cd ~/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/sysroot/usr/lib/

# 修改所有库文件的权限（添加执行权限，使其变绿）
chmod +x *.so*

# 检查一下是否变绿了
ls -l libSDL2_image-2.0.so.0
```
其实还是显示找不到，只能：
```
# 进入 lib64 目录
cd ~/RuyiSDKGames/SDLShooter-Hand/gnu-plct-venv/sysroot/usr/lib64/

# -s 表示创建软链接，-f 表示如果已存在则覆盖
ln -sf ../lib/libSDL2_image-2.0.so.0 .
ln -sf ../lib/libSDL2_image-2.0.so.0.900.0 .

# 批量创建 Mixer 的链接
ln -sf ../lib/libSDL2_mixer-2.0.so.0 .
ln -sf ../lib/libSDL2_mixer-2.0.so.0.900.0 .

# 批量创建 TTF 的链接（预防下一步报错）
ln -sf ../lib/libSDL2_ttf-2.0.so.0 .
ln -sf ../lib/libSDL2_ttf-2.0.so.0.900.0 .
```






