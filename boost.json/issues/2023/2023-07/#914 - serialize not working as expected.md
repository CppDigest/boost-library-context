# #914 - serialize not working as expected [Closed]

> Username: nursery01  
> Created at: 2023-07-11 11:52:26 UTC  
> Updated at: 2023-07-17 18:21:37 UTC  
> Closed at: 2023-07-17 18:21:37 UTC  
> Url: https://github.com/boostorg/json/issues/914  

BOOST_VERSION 108200  
BOOST_LIB_VERSION "1_82"  
  
```  
#include <iostream>  
#include <string>  
#include <boost/json.hpp>  
  
int main(){  
boost::json::object val;  
  
val["a"] = "123";  
val["b"] = 456;  
std::cout<<val.at("a")<<std::endl;  
std::string s=boost::json::serialize(val);  
std::cout<<s<<std::endl;  
std::cout<<"app end"<<std::endl;  
  return 0;  
}  
```  
  
output  
```  
"123"  
```  
![1](https://github.com/boostorg/json/assets/40170504/2b810325-ddee-42f0-bee5-624e7f33c07b)

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-07-11 11:59:07 UTC  
> Url: https://github.com/boostorg/json/issues/914#issuecomment-1630693804  

The assert fails in an stdio file, not in a Boost.JSON file. Judging by the file being fwrite, the problem is with stream output. I would need you to minimize the example (so, that I know which specific line results in the failed assert), and also I need a more specific description of your build environment: msvc version, target platform, build flags.

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-07-11 12:07:45 UTC  
> Url: https://github.com/boostorg/json/issues/914#issuecomment-1630706038  

Supposedly, one of these lines results in a failure:  
```c++  
std::string s=boost::json::serialize(val);  
std::cout<<s<<std::endl;  
```  
The former uses a Boost.JSON function, but should not involve fwrite.  
The latter does not use BOOST.JSON functions, and may use fwrite.

---

## Comment 3

> Username: nursery01  
> Created at: 2023-07-11 12:13:18 UTC  
> Url: https://github.com/boostorg/json/issues/914#issuecomment-1630714002  

This line will fail  
  
```  
std::cout<<s<<std::endl;  
```  
but,this is strange  
  
Microsoft (R) Incremental Linker Version 14.36.32535.0  
MSBuild version 17.6.3+07e294721  
windows11

---

## Comment 4

> Username: grisumbras  
> Created at: 2023-07-11 12:15:05 UTC  
> Url: https://github.com/boostorg/json/issues/914#issuecomment-1630716546  

Try analyzing the string in debugger. Is it malformed in any way?

---

## Comment 5

> Username: nursery01  
> Created at: 2023-07-13 06:58:44 UTC  
> Url: https://github.com/boostorg/json/issues/914#issuecomment-1633673543  

vc debug and gdb debug can`t get string values  
  
this is vc debug version  
```  
-------------------------------------------------------------------  
You may only use the C/C++ Extension for Visual Studio Code  
with Visual Studio Code, Visual Studio or Visual Studio for Mac  
software to help you develop and test your applications.  
-------------------------------------------------------------------  
-------------------------------------------------------------------  
You may only use the C/C++ Extension for Visual Studio Code  
with Visual Studio Code, Visual Studio or Visual Studio for Mac  
software to help you develop and test your applications.  
-------------------------------------------------------------------  
Loaded 'D:\c\test\test1\cmake\Debug\test1.exe'. Symbols loaded.  
Loaded 'C:\Windows\System32\ntdll.dll'.  
Loaded 'C:\Windows\System32\kernel32.dll'.  
Loaded 'C:\Windows\System32\KernelBase.dll'.  
Loaded 'C:\Windows\System32\msvcp140d.dll'.  
Loaded 'C:\Windows\System32\vcruntime140d.dll'.  
Loaded 'C:\Windows\System32\vcruntime140_1d.dll'.  
Loaded 'D:\c\test\test1\cmake\Debug\boost_json-vc143-mt-x64-1_82.dll'.  
Loaded 'C:\Windows\System32\ucrtbased.dll'.  
Unloaded 'C:\Windows\System32\ucrtbased.dll'.  
Loaded 'C:\Windows\System32\ucrtbased.dll'.  
Loaded 'C:\Windows\System32\ucrtbase.dll'.  
Loaded 'C:\Windows\System32\msvcp140.dll'.  
Loaded 'C:\Windows\System32\vcruntime140_1.dll'.  
Loaded 'C:\Windows\System32\vcruntime140.dll'.  
"123"  
Debug Assertion Failed!  
  
Program: D:\c\test\test1\cmake\Debug\test1.exe  
File: minkernel\crts\ucrt\src\appcrt\stdio\fwrite.cpp  
Line: 72  
  
Expression: buffer != nullptr  
  
For information on how your program can cause an assertion  
failure, see the Visual C++ documentation on asserts.  
  
(Press Retry to debug the application)  
Loaded 'C:\Windows\System32\kernel.appcore.dll'.  
Loaded 'C:\Windows\System32\msvcrt.dll'.  
Loaded 'C:\Windows\System32\user32.dll'.  
Loaded 'C:\Windows\System32\win32u.dll'.  
Loaded 'C:\Windows\System32\gdi32.dll'.  
Loaded 'C:\Windows\System32\gdi32full.dll'.  
Loaded 'C:\Windows\System32\msvcp_win.dll'.  
Loaded 'C:\Windows\System32\imm32.dll'.  
Loaded 'C:\Windows\System32\TextShaping.dll'.  
Loaded 'C:\Windows\System32\uxtheme.dll'.  
Loaded 'C:\Windows\System32\combase.dll'.  
Loaded 'C:\Windows\System32\rpcrt4.dll'.  
Loaded 'C:\Windows\System32\msctf.dll'.  
Loaded 'C:\Windows\System32\bcryptprimitives.dll'.  
Loaded 'C:\Windows\System32\sechost.dll'.  
Loaded 'C:\Windows\System32\TextInputFramework.dll'.  
Loaded 'C:\Windows\System32\oleaut32.dll'.  
Loaded 'C:\Windows\System32\CoreMessaging.dll'.  
Loaded 'C:\Windows\System32\CoreUIComponents.dll'.  
Loaded 'C:\Windows\System32\WinTypes.dll'.  
Loaded 'C:\Windows\System32\advapi32.dll'.  
Loaded 'C:\Windows\System32\cryptbase.dll'.  
Loaded 'C:\Windows\System32\ole32.dll'.  
The program '[9912] test1.exe' has exited with code 3 (0x3).  
  
  
```  
  
this is gdb debug  
```  
=thread-group-added,id="i1"  
Warning: Debuggee TargetArchitecture not detected, assuming x86_64.  
=cmd-param-changed,param="pagination",value="off"  
[New Thread 18328.0x59c8]  
[New Thread 18328.0x4ba4]  
[New Thread 18328.0x5ebc]  
[New Thread 18328.0x4a38]  
[New Thread 18328.0x58f8]  
[Thread 18328.0x4ba4 exited with code 3]  
[Thread 18328.0x5ebc exited with code 3]  
[Thread 18328.0x4a38 exited with code 3]  
```

---

## Comment 6

> Username: nursery01  
> Created at: 2023-07-17 06:52:20 UTC  
> Updated at: 2023-07-17 07:41:12 UTC  
> Url: https://github.com/boostorg/json/issues/914#issuecomment-1637474977  

I used vs to build today and run it, and   
it works correctly  
  
  
I used cmake+msvc before, and it run abnormally  
  
But I don’t know why it’s like this

---

## Comment 7

> Username: grisumbras  
> Created at: 2023-07-17 18:21:37 UTC  
> Url: https://github.com/boostorg/json/issues/914#issuecomment-1638647821  

This is probably due to incompatible build options between your Boost binaries and your program. I'm closing the issue as completed.
