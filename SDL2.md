### 安装 SDL2_mixer

安装 libxmp
```
cd ~/Downloads
# 克隆 libxmp 源码
git clone https://github.com/libxmp/libxmp.git
cd libxmp

# 建议创建一个 build 目录（如果它支持 cmake）或者使用标准的 autoconf
mkdir build && cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr
make -j$(nproc)
sudo make install

```
```
dnf install fluidsynth-devel opus-devel opusfile-devel libogg-devel wavpack-devel
```
```
git clone https://github.com/libsdl-org/SDL_mixer.git
cd SDL_mixer
git checkout SDL2
mkdir build && cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr
make -j$(nproc)
sudo make install
```

### 安装 SDL2_ttf
安装 freetype
```
sudo dnf install freetype-devel
```
```
# 对于 SDL_ttf
git clone https://github.com/libsdl-org/SDL_ttf.git
cd SDL_ttf
git checkout SDL2
mkdir build && cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr
make -j$(nproc)
sudo make install
```

### 安装 SDL2_image
```
sudo dnf install libpng-devel libjpeg-turbo-devel libwebp-devel libtiff-devel
```
```
sudo dnf install SDL2-static
```
```
# 下载源码 (建议从 GitHub 或官网下载最新稳定版)
git clone https://github.com/libsdl-org/SDL_image.git
cd SDL_image
git checkout SDL2

mkdir build && cd build

# 配置（会自动检测 RISC-V 环境）
cmake .. -DCMAKE_INSTALL_PREFIX=/usr

# 编译并安装
make -j$(nproc)
sudo make install
```
```
cmake .. -DCMAKE_INSTALL_PREFIX=/usr \
         -DSDL2_DIR=/usr/lib64/cmake/SDL2 \
         -DSDL2_INCLUDE_DIR=/usr/include/SDL2 \
         -DSDL2_LIBRARY=/usr/lib64/libSDL2.so
```
```
cd ~/Downloads/SDL_image/build
rm -rf *

# 显式关闭那些找不到的依赖，确保配置文件不再去搜它们 - 成功的
cmake .. -DCMAKE_INSTALL_PREFIX=/usr \
         -DSDL2IMAGE_AVIF=OFF \
         -DSDL2IMAGE_TIF=OFF \
         -DSDL2IMAGE_JXL=OFF

make -j$(nproc)
sudo make install
```


