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






