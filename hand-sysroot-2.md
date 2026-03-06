### 参照
https://github.com/qingwan12138/PLCT-WORK/blob/main/February/blog/ruyisdk%E4%BA%A4%E5%8F%89%E7%BC%96%E8%AF%91teeworlds.md

### 还要进入openEuler 然后安装
```
# 安装核心开发包（这是解决 crt1.o 报错的关键）
dnf install -y glibc-devel

# 为了保险起见，建议把基础开发包组都装上
dnf groupinstall -y "Development Tools"
```
### 进入 openEuler 手动安装依赖

