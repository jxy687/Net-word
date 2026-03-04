参照
https://github.com/jxy687/Net-word/blob/main/openEuler-sysroot.md
将 openEuler 移植到 sysroot 里面

### 获取项目
```
git clone https://github.com/udacity/CppND-Capstone-Snake-Game.git
```
```
cd CppND-Capstone-Snake-Game

mkdir build && cd build

# 清理缓存
rm -rf *

# 重新配置，增加静态链接参数
cmake -DCMAKE_TOOLCHAIN_FILE=$HOME/eclipse-workspace/Games/CppND-Capstone-Snake-Game/gnu-plct-venv/toolchain.cmake \
      -DCMAKE_SYSROOT=$HOME/eclipse-workspace/Games/CppND-Capstone-Snake-Game/gnu-plct-venv/sysroot \
      -DCMAKE_EXE_LINKER_FLAGS="-static-libstdc++ -static-libgcc" \
      ..

# 重新编译
make
```


```
# 激活虚拟环境
source ../gnu-plct-venv/bin/ruyi-activate

# 使用 qemu 的 -L 指定 sysroot 路径
ruyi-qemu -L ~/文档/Games/CppND-Capstone-Snake-Game/gnu-plct-venv/sysroot/ ./SnakeGame

```
