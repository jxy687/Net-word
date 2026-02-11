### 测试网络连通性
```
ping -c 4 www.baidu.com
```
<img width="959" height="577" alt="图片" src="https://github.com/user-attachments/assets/397b1c0a-34a6-4b0c-a375-ab201200e92c" />

```
curl -I http://www.baidu.com
```

成功返回：HTTP/1.1 200 OK

### 安装浏览器
```
dnf install firefox
```

可以成功打开，但是有点卡顿

```
dnf install epiphany
```

还是卡顿

## 解决方式一 ssh 转发

### 在虚拟机内修改 SSH 配置
```
sudo vi /etc/ssh/sshd_config
```
### 确保以下选项已开启（去掉前面的 # 号）：
```
X11Forwarding yes
X11UseLocalhost no
AddressFamily inet
```
### 重启 SSH 服务：
```
sudo systemctl restart sshd
```
### openEuler安装
```
sudo dnf install xauth -y
```
### 再次尝试
```
ssh -Y -p 12055 root@localhost epiphany
```
### 结果

<img width="816" height="681" alt="图片" src="https://github.com/user-attachments/assets/1616b988-f480-4eff-b700-ac9a1f20b10e" />

```
ssh -Y -p 12055 root@localhost "export WEBKIT_FORCE_SANDBOX=0; export NO_AT_BRIDGE=1; dbus-run-session epiphany"
```

成功

<img width="1920" height="1052" alt="图片" src="https://github.com/user-attachments/assets/9f5ff567-1556-496e-b83a-8dce5e144f03" />

但是还是卡顿，本质上还是在 openEuler 里面运行








