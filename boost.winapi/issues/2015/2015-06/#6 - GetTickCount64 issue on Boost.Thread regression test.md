# #6 - GetTickCount64 issue on Boost.Thread regression test [Closed]

> Username: viboes  
> Created at: 2015-06-29 06:21:00 UTC  
> Updated at: 2015-06-30 21:52:54 UTC  
> Closed at: 2015-06-29 18:38:21 UTC  
> Url: https://github.com/boostorg/winapi/issues/6  

Hi,  
  
I'm getting this error in http://www.boost.org/development/tests/develop/developer/output/teeks99-08a-win2012R2-64on64-boost-bin-v2-libs-thread-test-async__async_executor_p-test-msvc-8-0-dbg-adrs-mdl-64-thrd-mlt.html  
  
```  
    call "c:\users\boost\appdata\local\temp\boost_regression\b2_msvc_8.0_vcvarsall_amd64.cmd" >nul  
cl /Zm800 -nologo @"D:\t08\run\results\boost\bin.v2\libs\chrono\build\msvc-8.0\dbg\adrs-mdl-64\thrd-mlt\chrono.obj.rsp"   
  
chrono.cpp  
..\boost/detail/winapi/time.hpp(90) : error C2039: 'GetTickCount64' : is not a member of '`global namespace''  
..\boost/detail/winapi/time.hpp(90) : error C2873: 'GetTickCount64' : symbol cannot be used in a using-declaration  
  
```  
  
What has been changed and how can I fix it?

---

## Comment 1

> Username: Lastique  
> Created at: 2015-06-29 09:25:27 UTC  
> Url: https://github.com/boostorg/winapi/issues/6#issuecomment-116564815  

On 29.06.2015 09:21, Vicente J. Botet Escriba wrote:  
  
> Hi,  
>   
> I'm getting this error in  
> http://www.boost.org/development/tests/develop/developer/output/teeks99-08a-win2012R2-64on64-boost-bin-v2-libs-thread-test-async__async_executor_p-test-msvc-8-0-dbg-adrs-mdl-64-thrd-mlt.html  
>   
> |     call "c:\users\boost\appdata\local\temp\boost_regression\b2_msvc_8.0_vcvarsall_amd64.cmd" >nul  
> cl /Zm800 -nologo @"D:\t08\run\results\boost\bin.v2\libs\chrono\build\msvc-8.0\dbg\adrs-mdl-64\thrd-mlt\chrono.obj.rsp"  
>   
> chrono.cpp  
> ..\boost/detail/winapi/time.hpp(90) : error C2039: 'GetTickCount64' : is not a member of '`global namespace''  
> ..\boost/detail/winapi/time.hpp(90) : error C2873: 'GetTickCount64' : symbol cannot be used in a using-declaration  
>   
> |  
> |  
>   
> What has been changed and how can I fix it?  
  
The problem seems to be caused by the change of the default Windows API   
to Vista and later. The Windows SDK shipped with MSVC-8 does not support   
Vista API, and in your config.hpp you define BOOST_USE_WINDOWS_H which   
makes the real windows.h being used.  
  
I will see how to better fix this later today. One idea is to limit   
WinAPI version based on the compiler.

---

## Comment 2

> Username: Lastique  
> Created at: 2015-06-29 18:38:21 UTC  
> Url: https://github.com/boostorg/winapi/issues/6#issuecomment-116791605  

Fixed in https://github.com/boostorg/winapi/commit/b8e2ad5eb59a08427624788ccfca657d9206b9f4.

---

## Comment 3

> Username: viboes  
> Created at: 2015-06-30 21:52:54 UTC  
> Url: https://github.com/boostorg/winapi/issues/6#issuecomment-117355455  

Thanks.
