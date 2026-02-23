
### 开始

- 获取项目

```
git clone https://github.com/mevdschee/2048.c.git
```

- 使用 Eclipse 打开项目

<img width="1853" height="645" alt="2026-02-23 20-33-11屏幕截图" src="https://github.com/user-attachments/assets/37ed4eb6-7cf5-4cf9-b465-f7709cbe5654" />
<img width="891" height="729" alt="图片" src="https://github.com/user-attachments/assets/2582c7dc-535f-4b81-b988-53df3911f358" />


```
cd 2048.c
```

### 创建虚拟环境到项目目录

- RuyiSDK -> Venv -> new virtual environment
<img width="1854" height="1048" alt="图片" src="https://github.com/user-attachments/assets/048f986a-d19e-438b-99f6-89d7d19f7c08" />


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


