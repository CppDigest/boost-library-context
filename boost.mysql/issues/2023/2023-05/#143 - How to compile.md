# #143 - How to compile? [Closed]

> Username: venjin  
> Created at: 2023-05-11 12:03:16 UTC  
> Updated at: 2023-05-12 08:40:04 UTC  
> Closed at: 2023-05-11 12:41:49 UTC  
> Url: https://github.com/boostorg/mysql/issues/143  

boost1.82  
vs2022  
openssl1.1.1o install in C:\Program Files (x86)\OpenSSL-Win32  
use x86 Native Tools Command Prompt for VS 2022 run:  
bootstrap.bat  
b2.exe  
  
At end, i can not find the library like libboost_mysql-vc143-mt-gd-x32-1_82.lib at stage/libs dir. what is the right way to compile?

---

## Comment 1

> Username: anarthal  
> Created at: 2023-05-12 08:40:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/143#issuecomment-1545389370  

Right now, the library is header-only, so you don't need to build it separately.
