
### 更改系统时间，保证能连上网
```
timedatectl set-time 08:52:52
timedatectl set-time 2026-02-05
```


### 开始下载mate-desktop
```
sudo dnf install \
mate-session-manager \
mate-panel \
mate-desktop \
mate-desktop-configs \
marco \
caja \
xorg-x11-server-Xorg \
xorg-x11-xinit \
xorg-x11-apps \
xorg-x11-drivers \
dbus-x11 \
gnome-terminal
```
### 写启动文件（关键）
```
echo "exec mate-session" > ~/.xinitrc
```
```
startx
```
### 结果
```
[root@localhost ~]# startx
xauth:  file /root/.serverauth.1003 does not exist


X.Org X Server 1.20.11
X Protocol Version 11, Revision 0
Build Operating System: 9920f8491653 6.6.0-72.0.0.76.oe2403sp1.riscv64 
Current Operating System: Linux localhost.localdomain 6.6.0-98.0.0.103.oe2403sp2.riscv64 #1 SMP PREEMPT Fri Jun 27 10:45:15 UTC 2025 riscv64
Kernel command line: BOOT_IMAGE=/vmlinuz-6.6.0-98.0.0.103.oe2403sp2.riscv64 root=UUID=8606271e-bf1f-45bc-9cd6-ce88a7f5ac7f ro console=ttyS0 highres=off earlycon no4lvl systemd.default_timeout_start_sec=600 rootfstype=ext4 quiet net.ifnames=0 biosdevname=0 nmi_watchdog=1 rd.shell=0 selinux=0 crashkernel=256M reserve_kbox_mem=16M crash_kexec_post_notifiers panic=3 no-steal-time use-sched-idle-time
Build Date: 12 June 2025  07:33:49PM
Build ID: xorg-x11-server 1.20.11-38.oe2403sp2 
Current version of pixman: 0.42.2
	Before reporting problems, check http://wiki.x.org
	to make sure that you have the latest version.
Markers: (--) probed, (**) from config file, (==) default setting,
	(++) from command line, (!!) notice, (II) informational,
	(WW) warning, (EE) error, (NI) not implemented, (??) unknown.
(==) Log file: "/var/log/Xorg.0.log", Time: Mon Feb  9 23:56:21 2026
(==) Using system config directory "/usr/share/X11/xorg.conf.d"
(II) modeset(0): Initializing kms color map for depth 24, 8 bpc.
MESA-LOADER: failed to open zink: /usr/lib64/dri/zink_dri.so: cannot open shared object file: No such file or directory (search paths /usr/lib64/dri, suffix _dri)
failed to load driver: zink
MESA-LOADER: failed to open zink: /usr/lib64/dri/zink_dri.so: cannot open shared object file: No such file or directory (search paths /usr/lib64/dri, suffix _dri)
failed to load driver: zink
mate-session[1039]: WARNING: Unable to find provider '' of required component 'dock'
GNOME_KEYRING_CONTROL=/root/.cache/keyring-C2R8J3
SSH_AUTH_SOCK=/root/.cache/keyring-C2R8J3/ssh
Window manager warning: Failed to load theme "Menta": Failed to find a valid file for theme Menta

Window manager warning: Failed to load theme "Simple": Failed to find a valid file for theme Simple

Window manager warning: Log level 128: unsetenv() is not thread-safe and should not be used after threads are created
ALSA lib confmisc.c:855:(parse_card) cannot find card '0'
ALSA lib conf.c:5204:(_snd_config_evaluate) function snd_func_card_inum returned error: No such file or directory
ALSA lib confmisc.c:422:(snd_func_concat) error evaluating strings
ALSA lib conf.c:5204:(_snd_config_evaluate) function snd_func_concat returned error: No such file or directory
ALSA lib confmisc.c:1342:(snd_func_refer) error evaluating name
ALSA lib conf.c:5204:(_snd_config_evaluate) function snd_func_refer returned error: No such file or directory
ALSA lib conf.c:5727:(snd_config_expand) Evaluate error: No such file or directory
ALSA lib control.c:1570:(snd_ctl_open_noupdate) Invalid CTL default
[1770681394,000,xklavier.c:xkl_engine_start_listen/] 	The backend does not require manual layout management - but it is provided by the application

(mate-panel:1121): GLib-GIO-ERROR **: 23:56:39.155: Settings schema 'ca.desrt.dconf-editor.Settings' is not installed
mate-session[1039]: WARNING: Application 'mate-panel.desktop' failed to register before timeout
GNOME_KEYRING_CONTROL=/root/.cache/keyring-C2R8J3
SSH_AUTH_SOCK=/root/.cache/keyring-C2R8J3/ssh
GNOME_KEYRING_CONTROL=/root/.cache/keyring-C2R8J3
SSH_AUTH_SOCK=/root/.cache/keyring-C2R8J3/ssh
GNOME_KEYRING_CONTROL=/root/.cache/keyring-C2R8J3
SSH_AUTH_SOCK=/root/.cache/keyring-C2R8J3/ssh

(caja:1200): Gtk-WARNING **: 23:57:03.401: Failed to register client: GDBus.Error:org.gnome.SessionManager.AlreadyRegistered: Unable to register client

(polkit-mate-authentication-agent-1:1201): polkit-mate-1-WARNING **: 23:57:03.666: Unable to determine the session we are in: No session for pid 1201

(caja:1200): GLib-GObject-CRITICAL **: 23:57:07.018: ../gobject/gsignal.c:2522: signal 'middle_click' is invalid for instance '0x2ac7eccb30' of type 'FMDesktopIconView'

(caja:1200): GLib-GIO-CRITICAL **: 23:57:41.933: GFileInfo created without standard::edit-name

(caja:1200): GLib-GIO-CRITICAL **: 23:57:41.935: file ../gio/gfileinfo.c: line 1743 (g_file_info_get_edit_name): should not be reached

(caja:1200): GLib-GIO-CRITICAL **: 23:57:41.937: GFileInfo created without standard::is-symlink

(caja:1200): GLib-GIO-CRITICAL **: 23:57:41.937: file ../gio/gfileinfo.c: line 1677 (g_file_info_get_is_symlink): should not be reached

(caja:1200): GLib-GIO-CRITICAL **: 23:57:41.938: GFileInfo created without standard::is-hidden

(caja:1200): GLib-GIO-CRITICAL **: 23:57:41.938: file ../gio/gfileinfo.c: line 1633 (g_file_info_get_is_hidden): should not be reached

(caja:1200): GLib-GIO-CRITICAL **: 23:57:41.938: GFileInfo created without standard::is-backup
```
### 修复 (mate-panel:1121): GLib-GIO-ERROR **: 23:56:39.155: Settings schema 'ca.desrt.dconf-editor.Settings' is not installed
```
sudo dnf install dconf-editor
```
