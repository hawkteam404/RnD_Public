# Firmware Information
**Device**: N200RE V5_Firmware
**Firmware**: V9.3.5u.6437_B20230519
**URL**: https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/204/ids/36.html

# Description and Impact
Function `setWanCfg` executes os command `echo` to write hostname into system. An attacker can craft payload chains with `CVE-2023-4746` to bypass data validation in function `Validity_check` to execute OS command remotely. This vulnerability is a variant of CVE-2023-4412.

# Root-cause
Program `cstecgi.cgi` handles HTTP request from client. Parameter hostName is taken from client’s request
![image](https://github.com/user-attachments/assets/7abfba6a-db00-4b25-9d0a-d063f184be8a)

Value of parameter hostname is validated by function `Validity_check`. However, this function is vulnerable to **CVE-2023-4746** which attacker can craft a specific payload to bypass validation check.
![image](https://github.com/user-attachments/assets/7799114c-b18f-4889-a25a-19a420557fbf)

Function `doSystem` executes OS command with crafted payload.
![image](https://github.com/user-attachments/assets/79e567c7-0d4f-45f9-b1d5-bd1d0a12d593)

# References
- https://nvd.nist.gov/vuln/detail/CVE-2023-4412
-	https://nvd.nist.gov/vuln/detail/CVE-2023-4746
