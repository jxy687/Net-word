
```
# 创建目标 sysroot 目录（若不同名请替换）
mkdir -p ~/文档/Games/CppND-Capstone-Snake-Game/gnu-plct-venv/sysroot

# 同步常用运行时库和用户空间（保留符号链接/权限/ACL/xattrs）
rsync -aHAX --numeric-ids -e "ssh -p 12055" \
  root@localhost:/lib/ \
  ~/文档/Games/CppND-Capstone-Snake-Game/gnu-plct-venv/sysroot/lib/

rsync -aHAX --numeric-ids -e "ssh -p 12055" \
  root@localhost:/lib64/ \
  ~/文档/Games/CppND-Capstone-Snake-Game/gnu-plct-venv/sysroot/lib64/

rsync -aHAX --numeric-ids -e "ssh -p 12055" \
  root@localhost:/usr/lib/ \
  ~/文档/Games/CppND-Capstone-Snake-Game/gnu-plct-venv/sysroot/usr/lib/

rsync -aHAX --numeric-ids -e "ssh -p 12055" \
  root@localhost:/usr/lib64/ \
  ~/文档/Games/CppND-Capstone-Snake-Game/gnu-plct-venv/sysroot/usr/lib64/

# 可选：同步 /usr/bin, /usr/sbin（某些 helper 程序可能需要）
rsync -aHAX --numeric-ids -e "ssh -p 12055" \
  root@localhost:/usr/bin/ \
  ~/文档/Games/CppND-Capstone-Snake-Game/gnu-plct-venv/sysroot/usr/bin/

rsync -aHAX --numeric-ids -e "ssh -p 12055" \
  root@localhost:/usr/sbin/ \
  ~/文档/Games/CppND-Capstone-Snake-Game/gnu-plct-venv/sysroot/usr/sbin/
```
