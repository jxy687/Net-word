```
source ./gnu-plct-venv/bin/ruyi-activate

riscv64-plct-linux-gnu-readelf -d ./build/SDLShooter-Linux | grep -E "NEEDED|RPATH|RUNPATH"

riscv64-plct-linux-gnu-ldd --root=$PWD/gnu-plct-venv/sysroot ./build/SDLShooter-Linux

```
追踪失败原因
```
env SDL_AUDIODRIVER=dummy LIBGL_ALWAYS_SOFTWARE=1 \
strace -f -e trace=openat,access ruyi-qemu -L $PWD/gnu-plct-venv/sysroot ./build/SDLShooter-Linux
```
尝试用 -E 把所有可能的库路径都喂给 QEMU，避免它去读取错误的路径：
```
env SDL_AUDIODRIVER=dummy LIBGL_ALWAYS_SOFTWARE=1 \
ruyi-qemu -L $PWD/gnu-plct-venv/sysroot \
-E LD_LIBRARY_PATH=/lib64/lp64d:/usr/lib64:/usr/lib \
./build/SDLShooter-Linux
```
```
riscv64-plct-linux-gnu-readelf -d ~/RuyiSDKGames/SDLShooter-Hand-2-1/gnu-plct-venv/sysroot/usr/lib64/libpng16.so.16.40.0 | grep NEEDED
```
可以看到查找过程：
```
«Ruyi gnu-plct-venv» jxy@jxy-ThinkCentre-M828z-D101:~/RuyiSDKGames/SDLShooter-Hand-2-1$ env SDL_AUDIODRIVER=dummy LIBGL_ALWAYS_SOFTWARE=1 \
ruyi-qemu -L $PWD/gnu-plct-venv/sysroot \
-E LD_DEBUG=libs \
./build/SDLShooter-Linux 2>&1 | tee dynamic_link.log

```
<img width="1857" height="581" alt="image" src="https://github.com/user-attachments/assets/3178e6a6-2ec2-492e-9748-90ec6a4746eb" />
```
«Ruyi gnu-plct-venv» jxy@jxy-ThinkCentre-M828z-D101:~/RuyiSDKGames/SDLShooter-Hand-2-1$ env SDL_AUDIODRIVER=dummy LIBGL_ALWAYS_SOFTWARE=1 \
ruyi-qemu -L $PWD/gnu-plct-venv/sysroot \
-E LD_DEBUG=bindings \
./build/SDLShooter-Linux 2>&1 | tee symbol_bind.log

```
```
env SDL_AUDIODRIVER=dummy LIBGL_ALWAYS_SOFTWARE=1 \
strace -f -e trace=openat,access ruyi-qemu -L $PWD/gnu-plct-venv/sysroot ./build/SDLShooter-Linux 2>&1 | grep ENOENT
```
使用gdb，发现是libc.so.6太多了，使用的是创建虚拟环境时自带的，所以，hand-sysroot.md 第一步应该把sysroot下的内容完全删除


