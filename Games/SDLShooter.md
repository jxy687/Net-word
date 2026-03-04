参照
https://github.com/jxy687/Net-word/blob/main/SDL2.md
在 openEuler 中安装好 SDL2 相关库

获取项目
```
git clone https://github.com/WispSnow/SDLShooter.git

cd SDLShooter

mkdir build && cd build

cmake

make

cd ..

./build/SDLShooter-Linux
```
会显示驱动相关的错误
```
export SDL_AUDIODRIVER=dummy

export LIBGL_ALWAYS_SOFTWARE=1

./build/SDLShooter-Linux
```

