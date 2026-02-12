### 准备环境

```
sudo dnf install -y gcc-c++ cmake SDL2-devel wget unzip
```

### 开始

- 下载并解压，[项目地址](https://github.com/udacity/CppND-Capstone-Snake-Game)

```
wget https://github.com/udacity/CppND-Capstone-Snake-Game/archive/refs/heads/master.zip
unzip master.zip
cd CppND-Capstone-Snake-Game-master
```

- 编译

```
mkdir build && cd build
cmake ..
make
```

<img width="966" height="407" alt="图片" src="https://github.com/user-attachments/assets/abeb92f8-f9bb-4fa0-8de6-3120599fa22f" />


- 运行

```
./SnakeGame
```

- 示意图

<img width="1849" height="887" alt="图片" src="https://github.com/user-attachments/assets/29452a69-5ed3-48e7-aeeb-cc85abe39420" />
