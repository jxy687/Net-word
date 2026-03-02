## 开始

### 在 qemu 上的 openEluer 中
```
git clone https://github.com/troglobit/tetris.git
cd tetris
make
./tetris
```

### 在 RuyiSDK 中
```
git clone https://github.com/troglobit/tetris.git
cd tetris

```

打开 eclipse 中的命令行
```
ruyi venv -t gnu-plct-xthead -e qemu-user-riscv-xthead sipeed-lpi4a ./sipeed-xthead-venv

source ./sipeed-xthead-venv/bin/ruyi-activate
```
使用虚拟环境里面的 gcc
```
# 清理之前的本地编译记录（如果有）
make clean

# 使用交叉编译器进行编译
# 我们通过 CC 变量指定虚拟环境中的编译器
make CC=riscv64-plctxthead-linux-gnu-gcc
```
检查产物格式
```
file ./tetris
```
```
ruyi-qemu ./tetris #目前只有 qemu-user-riscv-xthead 这个模拟器可以
```
运行结果
<img width="1181" height="669" alt="image" src="https://github.com/user-attachments/assets/327d5748-14c3-400c-b83a-dd8bde531f85" />

