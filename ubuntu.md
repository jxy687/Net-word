### 切换为英文
```
# 1. 安装英文语言包（如未安装）
sudo apt update
sudo apt install language-pack-en

# 2. 生成并设置英文 locale
sudo locale-gen en_US.UTF-8
sudo update-locale LANG=en_US.UTF-8 LANGUAGE=en_US:en LC_ALL=en_US.UTF-8

# 3. 重启生效
sudo reboot
```

### 切换为中文
```
# 1. 安装中文语言包（如未安装）
sudo apt update
sudo apt install language-pack-zh-hans

# 2. 生成并设置中文 locale
sudo locale-gen zh_CN.UTF-8
sudo update-locale LANG=zh_CN.UTF-8 LANGUAGE=zh_CN:zh LC_ALL=zh_CN.UTF-8

# 3. 重启生效
sudo reboot

```
