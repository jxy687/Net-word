### 使用vscode 的ssh FS插件，ssh-remote插件不支持risc-v架构

#### 下载完成后编写配置文件

<img width="1839" height="1044" alt="image" src="https://github.com/user-attachments/assets/666e9ebd-2738-43ba-bbbb-43f28b6ac5ca" />

具体:  
name:openEuler-riscv  
host:127.0.0.1  
port:12055 #qemu的配置文件里面设置的  
username:root  
password:openEuler12#$  
然后点击save  

#### 点击文件夹图标，将openEuler的文件系统显示在vscode的项目目录里面

<img width="550" height="1000" alt="image" src="https://github.com/user-attachments/assets/f84c48e9-449a-4c0f-9659-7a78994c2aa4" />

成功的结果是CONNECTIONS显示有内容，并且vscode的项目目录可以看到openEuler的文件系统

<img width="550" height="1000" alt="image" src="https://github.com/user-attachments/assets/21b507f3-310b-4372-89b5-07d1f5e4f05e" />

其中，Projects是本地的项目，SSH FS - openeuler-riscv是虚拟机的文件系统


