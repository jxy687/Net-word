
### 开始

- 获取项目

```
git clone https://github.com/mevdschee/2048.c.git
```

- 使用 Eclipse 打开项目

```
cd 2048.c
```

### 创建虚拟环境到项目目录

- 创建虚拟环境

- 打开终端

- 激活虚拟环境

```
source ./.venv/bin/ruyi-activate
```
```
ruyi venv -t llvm-upstream -e qemu-user-riscv-upstream --sysroot-from gnu-plct generic ./llvm-venv
source ./llvm-venv/bin/ruyi-activate
不行
```

```
ruyi venv -t gnu-plct -e qemu-user-riscv-upstream generic ./gnu-plct-venv
source ./gnu-plct-venv/bin/ruyi-activate
riscv64-plct-linux-gnu-gcc ./2048.c -o 2048
ruyi-qemu ./2048
```
```
ruyi venv -t gnu-plct-xthead -e qemu-user-riscv-xthead sipeed-lpi4a ./sipeed-xthead-venv
source ./sipeed-xthead-venv/bin/ruyi-activate
riscv64-plctxthead-linux-gnu-gcc ./2048.c -o 2048
ruyi-qemu ./2048

```


