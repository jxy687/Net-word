参照
https://github.com/jxy687/Net-word/blob/main/openEuler-sysroot.md
将 openEuler 移植到 sysroot 里面

```
# 使用 qemu 的 -L 指定 sysroot 路径，配合 ldd 观察
ruyi-qemu -L ~/文档/Games/CppND-Capstone-Snake-Game/gnu-plct-venv/sysroot/ ./SnakeGame

```
