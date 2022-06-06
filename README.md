# How to change background color of Ly

The background color cannot be changed by editing the Ly config file at `/etc/ly/config.ini`. The `bg` variable in that config file refers only to the dialog box and the 'menu bar' at the top. The actual background of the entire screen will always be black no matter the `bg` value. 

Confusing... I know. 😬

To edit the background color we need to edit the file at `/lib/systemd/system/ly.service` and add the following in:
```
ExecStartPre=/usr/bin/printf '%%b' '\e]P0FFC5C4\e]P7FFFFFF\ec'
```
Like so:
```
[Unit]
Description=TUI display manager
After=systemd-user-sessions.service plymouth-quit-wait.service
After=getty@tty2.service

[Service]
Type=idle
ExecStartPre=/usr/bin/printf '%%b' '\e]P0FFC5C4\e]P7FFFFFF\ec'
ExecStart=/usr/bin/ly
StandardInput=tty
TTYPath=/dev/tty2
TTYReset=yes
TTYVHangup=yes

[Install]
Alias=display-manager.service
```
The above example sets color0(bg) to `#FFC5C4` and color7(fg) to `#FFFFFF`.

Now when you reboot the colors should've changed. Cool!

But if you log back out, you will notice the colors have reverted back to the default black and white colors. 😥

**Let's fix that!**

In `/etc/ly/config.ini` uncomment `term_reset_cmd` and add the following:
```
/usr/bin/printf '%%b' '\e]P0FFC5C4\e]P7FFFFFF\ec'
```
Like so:
```
term_reset_cmd = /usr/bin/tput reset; /usr/bin/printf '%%b' '\e]P0FFC5C4\e]P7FFFFFF\ec'
```
Now reboot and you should be good to go! 🤩

[Source](https://github.com/fairyglade/ly/issues/146)
