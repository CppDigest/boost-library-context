# #361 - Compiler Error using VS2019 and Boost 1.73 [Closed]

> Username: KnoerleMan  
> Created at: 2020-09-19 15:52:37 UTC  
> Updated at: 2020-10-08 08:36:42 UTC  
> Closed at: 2020-10-08 08:36:42 UTC  
> Url: https://github.com/boostorg/asio/issues/361  

When using Boost Asio Deadline Timer in application created with Visual Studio 2019 i get the following error, using the Boost 1.73 Package (installed via vcpkg):  
  
"C2678: binary operator "==": no operator could be found, which accepts a left-sided operand of the type "const Executor" (or no suitable conversion is possible), C:\vcpkg\installed\x86-windows\include\boost\asio\impl\executor.hpp	218	"  
  
Source of failure:  
[https://github.com/boostorg/asio/blob/b3c8f8ed5e8b7b56692ce38bec7fd1e725a90d43/include/boost/asio/impl/executor.hpp#L128]()  
  
This issue already got mentioned on some github pages, but there seems to be no fix, except using older versions.  
  
Anybody with further knowledge?
