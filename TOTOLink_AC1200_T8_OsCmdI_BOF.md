# Device info

**Device name**: AC1200 T8

**Version**: V4.1.5cu.862_B20230228

**Firmware download**: https://www.totolink.net/home/menu/detail/menu_listtpl/download/id/222/ids/36.html

# OS command injection in **setDiagnosisCfg**

## Root-cause
- Command `ping` is crafted using `sprintf` and executed using function `system`. An attacker can craft specific payload to manipulate system's command.
- Function `Validity_check` that checks if variable contains any special characters. This function can be bypassed allowing attacker inject custom commands.

![image](https://github.com/user-attachments/assets/a7049a7f-c99f-4f41-bcd5-ea6642c01c68)

## Recommendations
- Use function `execve` instead of `system`

# Buffer overflow in **setDiagnosisCfg**

# Root-cause
- Variable that's used to craft command is an array that has length 128. Firmware is using functino `sprintf` to craft a string and save this string to array without checking variable's size.

![image](https://github.com/user-attachments/assets/23a2a69b-d293-4b2d-aa75-b39f47a53ad2)

## Recommendations
- Use function `snprintf` that checks length of destination variable

# OS command injection in **setTracerouteCfg**

## Root-cause
- Command `tracert` is crafted using `sprintf` and executed using function `system`. An attacker can craft specific payload to manipulate system's command.
- Function `Validity_check` that checks if variable contains any special characters. This function can be bypassed allowing attacker inject custom commands.

![image](https://github.com/user-attachments/assets/7d64064f-7b9f-43bf-bfcd-2146d84b46c7)

## Recommendation
- Use function `execve` instead of `system`

# Buffer overflow in **setTracerouteCfg**

# Root-cause
- Variable that's used to craft command is an array that has length 128. Firmware is using functino `sprintf` to craft a string and save this string to array without checking variable's size.

![image](https://github.com/user-attachments/assets/da0b1169-ef27-44d3-b2ee-8d8dc8d03277)


## Recommendations
- Use function `snprintf` that checks length of destination variable
