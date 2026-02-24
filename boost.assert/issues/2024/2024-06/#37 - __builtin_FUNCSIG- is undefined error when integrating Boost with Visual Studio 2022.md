# #37 - __builtin_FUNCSIG" is undefined error when integrating Boost with Visual Studio 2022 [Closed]

> Username: yuantangjin  
> Created at: 2024-06-08 02:23:35 UTC  
> Updated at: 2024-11-07 09:19:48 UTC  
> Closed at: 2024-06-11 15:10:15 UTC  
> Url: https://github.com/boostorg/assert/issues/37  

Hello everyone,  
  
I'm working on a Windows 10 system using Visual Studio 2022 as my IDE. I installed the Boost library and integrated it into my Visual Studio project using the following commands:  
  
```  
vcpkg install boost  
vcpkg integrate install  
```  
  
However, I'm encountering an issue. Whenever I include either of the following lines in my project:  
```  
#include <boost/variant.hpp>  
#include <boost/algorithm/string.hpp>  
```  
  
I get the following error:  
```  
Severity    Code    Description    Project    File    Line    Suppression State  
Error       identifier "__builtin_FUNCSIG" is undefined    D:\code\tool\test\vcpkg\installed\x64-windows\include\boost\throw_exception.hpp    224  
```  
  
Here are the detailed messages from the Visual Studio 2022 Output window:  
```  
1>        D:\code\tool\test\vcpkg\installed\x64-windows\include\boost/throw_exception.hpp(224): error : identifier "__builtin_FUNCSIG" is undefined  
1>  
1>        1 error detected in the compilation of "E:/code/yhello.cu".  
1>        yhello.cu  
1>        D:\Visual studio\install_root\Microsoft Visual Studio\2022\Community\MSBuild\Microsoft\VC\v170\BuildCustomizations\CUDA 11.7.targets(790,9): error MSB3721: The command ""C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.7\bin\nvcc.exe" -gencode=arch=compute_52,code=\"sm_52,compute_52\" --use-local-env -ccbin "D:\Visual studio\install_root\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.36.32532\bin\HostX64\x64" -x cu   -I"C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.7\include" -I"D:\code\tool\test\vcpkg\installed\x64-windows\include" -I"C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.7\include"  -G   --keep-dir x64\Debug  -maxrregcount=0  --machine 64 --compile -cudart static  -g  -DWIN32 -DWIN64 -D_DEBUG -D_CONSOLE -D_MBCS -Xcompiler "/EHsc /W3 /nologo /Od /Fdx64\Debug\vc143.pdb /FS /Zi /RTC1 /MDd " -o "E:\code\x64\Debug\yhello.cu.obj" "E:\code\yhello.cu"” exited with code 2.  
1>      Task "CudaCompile" execution - FAILED.  
  
```  
  
I think that the issue might be resolved by using an older version of the Boost library, specifically Boost 1.70.0. However, I've found it challenging to install this specific version using vcpkg, as it defaults to installing the latest Boost version.  
  
I'm using Visual Studio 2022 and not Visual Studio Code, which makes it less straightforward to use JSON configuration files or CMake scripts directly. I prefer a simpler method to install the Boost library and integrate it with Visual Studio so that I can develop and debug my project directly within the IDE.  
  
Could someone guide me on how to install Boost 1.70.0 and integrate it into Visual Studio 2022?  
Thank you for your help!

---

## Comment 1

> Username: pdimov  
> Created at: 2024-06-09 10:59:51 UTC  
> Url: https://github.com/boostorg/assert/issues/37#issuecomment-2156443035  

It looks like the nVidia compiler doesn't recognize `__builtin_FUNCSIG`.  
  
You shouldn't need to go back as far as 1.70.0; the use of `__builtin_FUNCSIG` is a recent change, made in 1.85.0 (https://github.com/boostorg/assert/commit/447e0b3a331930f8708ade0e42683d12de9dfbc3). To use an earlier Boost version (such as 1.84) via vcpkg, follow these instructions:  
  
https://learn.microsoft.com/en-us/vcpkg/consume/boost-versions  
  
However, before you do that, can you please try something with your current 1.85 installation? Change this line  
  
https://github.com/boostorg/assert/blob/425b5ba31b0667439fb72dbf9ce425af6ef1b094/include/boost/assert/source_location.hpp#L147  
  
from  
```  
#elif defined(BOOST_MSVC) && BOOST_MSVC >= 1935  
```  
to  
```  
#elif defined(BOOST_MSVC) && BOOST_MSVC >= 1935 && !defined(__NVCC__)  
```  
and report back if this fixes the problem.  
  
If it does, I'll include the fix in the next Boost release.

---

## Comment 2

> Username: yuantangjin  
> Created at: 2024-06-11 15:09:34 UTC  
> Url: https://github.com/boostorg/assert/issues/37#issuecomment-2161008855  

> It looks like the nVidia compiler doesn't recognize `__builtin_FUNCSIG`.  
>   
> You shouldn't need to go back as far as 1.70.0; the use of `__builtin_FUNCSIG` is a recent change, made in 1.85.0 ([447e0b3](https://github.com/boostorg/assert/commit/447e0b3a331930f8708ade0e42683d12de9dfbc3)). To use an earlier Boost version (such as 1.84) via vcpkg, follow these instructions:  
>   
> https://learn.microsoft.com/en-us/vcpkg/consume/boost-versions  
>   
> However, before you do that, can you please try something with your current 1.85 installation? Change this line  
>   
> https://github.com/boostorg/assert/blob/425b5ba31b0667439fb72dbf9ce425af6ef1b094/include/boost/assert/source_location.hpp#L147  
>   
> from  
>   
> ```  
> #elif defined(BOOST_MSVC) && BOOST_MSVC >= 1935  
> ```  
>   
> to  
>   
> ```  
> #elif defined(BOOST_MSVC) && BOOST_MSVC >= 1935 && !defined(__NVCC__)  
> ```  
>   
> and report back if this fixes the problem.  
>   
> If it does, I'll include the fix in the next Boost release.  
  
Thank you very much for your guidance. I have resolved the issue, though my approach was slightly different from the one you provided. You might find this method more straightforward and effective: https://stackoverflow.com/questions/78594552/builtin-funcsig-is-undefined-error-when-integrating-boost-with-visual-studio?noredirect=1#comment138562340_78594552. The method suggested by this user is also a good alternative.  
  
Once again, thank you for your assistance!

---

## Comment 3

> Username: sbcao  
> Created at: 2024-11-07 09:19:47 UTC  
> Url: https://github.com/boostorg/assert/issues/37#issuecomment-2461713575  

After trying to change the boost/assert/source_location.hpp file, the error is still not resolved, can I only change the current boost version?  
![Image](https://github.com/user-attachments/assets/04988abb-61c5-44e7-bb15-9e63af9ab37f)
