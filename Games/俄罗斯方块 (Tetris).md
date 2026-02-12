### 暂时还不行

### 配置好必要的环境

```
sudo dnf install -y gcc make git
sudo dnf install -y SDL2-devel SDL2_ttf-devel SDL2_image-devel
```

### 开始

- 获取项目

```
git clone https://github.com/willyb321/tetris-sdl2.git
```

- 进入项目

```
cd tetris-sdl2
```

- 针对 RISC-V 编译

```
gcc -o tetris main.c -lSDL2 -lSDL2_ttf
```

- 开始游玩

```
./tetris
```

- 示意图

