# Production info and details
## Software
- REVE Antivirus For Linux (64 bit)
- Environment: Ubuntu 24.04.1 LTS (amd64)
- Version: 1.0.0.0
- Download link: https://www.reveantivirus.com/en/download
## Web App
- https://service.reveantivirus.com/
- https://myaccount.reveantivirus.com/

# Vulnerabilities
## Reflected XSS on service.reveantivirus.com
### Description and impact
`https://myaccount.reveantivirus.com/login/login.jsp` is a login function that has parameter `mess` to show message on client's browser. An attacker can manipuate this parameter, inject malicious Javascript code.

### Step to reprocedure
1. Go to `https://myaccount.reveantivirus.com/endpointcloud/login/login.jsp`, enter any invalid email and password, error message is shown with parameter `mess`
![image](https://github.com/user-attachments/assets/daab00cb-3b3a-46ae-90bc-1b000c478a82)

2. Inject payload `<script>alert(1);</script>` to parameter `mess`. Injected Javascript code will be executed
![image](https://github.com/user-attachments/assets/b258e9d4-19cc-4874-a6b1-abbb2d31c177)


## Reflected XSS on myaccount.reveantivirus.com
### Description and impact
`https://service.reveantivirus.com/REVEAntivirus/login/login.jsp` is a login function that has parameter `mess` to show message on client's browser. An attacker can manipuate this parameter, inject malicious Javascript code.

### Step to reprocedure
1. Go to `https://service.reveantivirus.com/`. Website redirects to `https://service.reveantivirus.com/REVEAntivirus/login/login.jsp?mess=Please%20login` automatically
![image](https://github.com/user-attachments/assets/0c59377a-b4ff-4eb2-8e10-60039416476f)

2. Inject payload `<script>alert(1);</script>` to parameter `mess`. Injected Javascript code will be executed
![image](https://github.com/user-attachments/assets/0f59bcea-48f3-4a6b-b701-e707ea4fadd0)


## Insecure default file permission leads to privilege escalation
### Description and impact
REVE Antivirus for Linux has writable system service units in `/etc/inid.d/`. Any unprivileged user can overwrite these files, execute malicious code as root after system reboot.

### Root-cause
By default, system service units of REVE Antivirus has permission 777 in `/etc/init.d/`.
![image](https://github.com/user-attachments/assets/3899ae34-7072-4ed2-8066-f317c66c87c4)

It's possibly because REVE runs bash script `/usr/local/reveantivirus/tmp/reveinstall` as a post-install script to install its services inside system

![image](https://github.com/user-attachments/assets/ffa8f121-3aea-4c8e-a3a9-acad26430f38)

### Step to reprocedure
1. Inject payload `id > /tmp/compromised` to `/etc/init.d/revexscanD`
![image](https://github.com/user-attachments/assets/11465f4b-1f6f-460b-8abf-92d4b9165c77)

2. Reboot system. Payload is executed
![image](https://github.com/user-attachments/assets/7d96b58e-ea25-4ae4-af1c-97e1b6e94612)
