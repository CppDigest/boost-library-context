# #606 - runtime-link=static link=shared does nothing [Open]

> Username: andry81  
> Created at: 2020-05-14 07:07:17 UTC  
> Updated at: 2021-05-29 17:22:23 UTC  
> Url: https://github.com/boostorg/build/issues/606  

boost version: `1.72.0`  
  
I want to build boost dlls with static runtime to link into an executable with another dll with static runtime. Another dll is a protobuf dll which I have to link with static runtime to resolve some issue around a static data constructor which internally accessing protobuf globals.  
  
The output:  
```  
...found 1 target...  
  
  
The Boost C++ Libraries were successfully built!  
  
The following directory should be added to compiler include paths:  
  
    D:\_3dparty\vc2017_x86\utility\boost\boost-1_72_0-release  
  
The following directory should be added to linker library paths:  
  
    D:\_3dparty\vc2017_x86\utility\boost\boost-1_72_0-release\stage\lib  
```  
  
But nothing else happens.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-05-14 13:33:03 UTC  
> Url: https://github.com/boostorg/build/issues/606#issuecomment-628638073  

Can you provide the full command you used to build with options `-a -d2 --debug-configuration` added?

---

## Comment 2

> Username: andry81  
> Created at: 2020-05-14 18:19:05 UTC  
> Url: https://github.com/boostorg/build/issues/606#issuecomment-628806819  

I didn't use anything else:  
```  
b2.exe runtime-link=static link=shared  
```

---

## Comment 3

> Username: grafikrobot  
> Created at: 2020-05-14 18:56:03 UTC  
> Url: https://github.com/boostorg/build/issues/606#issuecomment-628826149  

> I didn't use anything else:  
>   
> ```  
> b2.exe runtime-link=static link=shared  
> ```  
  
Sorry, I meant to also ask to include output from running it. Can you run this:  
  
```  
b2.exe runtime-link=static link=shared -a -d2 --debug-configuration  
```  
And attach the output here. Warning, it will be a lot of output.

---

## Comment 4

> Username: andry81  
> Created at: 2020-05-15 05:53:43 UTC  
> Url: https://github.com/boostorg/build/issues/606#issuecomment-629042756  

```  
notice: found boost-build.jam at D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/boost-build.jam  
notice: loading Boost.Build from D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src  
notice: Searching 'C:\Windows' 'C:\Users\Andry' 'C:\Users\Andry' 'D:\_3dparty\vc2017_x86\utility\boost\boost-1_72_0-release\tools/build/src' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/kernel' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/util' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/build' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/tools' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/contrib' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/.' for site-config configuration file 'site-config.jam'.  
notice: Configuration file 'site-config.jam' not found in 'C:\Windows' 'C:\Users\Andry' 'C:\Users\Andry' 'D:\_3dparty\vc2017_x86\utility\boost\boost-1_72_0-release\tools/build/src' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/kernel' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/util' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/build' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/tools' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/contrib' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/.'.  
notice: Searching 'C:\Users\Andry' 'C:\Users\Andry' 'D:\_3dparty\vc2017_x86\utility\boost\boost-1_72_0-release\tools/build/src' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/kernel' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/util' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/build' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/tools' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/contrib' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/.' for user-config configuration file 'user-config.jam'.  
notice: Configuration file 'user-config.jam' not found in 'C:\Users\Andry' 'C:\Users\Andry' 'D:\_3dparty\vc2017_x86\utility\boost\boost-1_72_0-release\tools/build/src' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/kernel' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/util' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/build' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/tools' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/contrib' 'D:/_3dparty/vc2017_x86/utility/boost/boost-1_72_0-release/tools/build/src/.'.  
notice: Searching '.' for project-config configuration file 'project-config.jam'.  
notice: Loading project-config configuration file 'project-config.jam' from '.'.  
notice: [msvc-cfg] msvc-14.0 detected, command: 'C:\Programs\Dev\Microsoft\Visual Studio 2015\VC\bin\cl.exe'  
notice: [msvc-cfg] msvc-14.1 detected, command: 'C:\Programs\Dev\Microsoft\Visual Studio 2017\Professional\VC\Tools\MSVC\14.16.27023\bin\Hostx64\x64\cl.exe'  
notice: will use 'C:\Programs\Dev\Microsoft\Visual Studio 2017\Professional\VC\Tools\MSVC\14.16.27023\bin\Hostx64\x64\cl.exe' for msvc, condition <toolset>msvc-14.1  
'notice: [generate-setup-cmd] 14.16.27023 is 14.1'  
'notice: [generate-setup-cmd] 14.16.27023 is 14.1'  
'notice: [generate-setup-cmd] 14.16.27023 is 14.1'  
'notice: [generate-setup-cmd] 14.16.27023 is 14.1'  
'notice: [generate-setup-cmd] 14.16.27023 is 14.1'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>/<address-model>', setup: 'C:\Programs\Dev\Microsoft\Visual Studio 2017\Professional\VC\Auxiliary\Build\vcvars32.bat'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>/<address-model>32', setup: 'C:\Programs\Dev\Microsoft\Visual Studio 2017\Professional\VC\Auxiliary\Build\vcvars32.bat'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>x86/<address-model>', setup: 'C:\Programs\Dev\Microsoft\Visual Studio 2017\Professional\VC\Auxiliary\Build\vcvars32.bat'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>x86/<address-model>32', setup: 'C:\Programs\Dev\Microsoft\Visual Studio 2017\Professional\VC\Auxiliary\Build\vcvars32.bat'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>/<address-model>64', setup: 'C:\Programs\Dev\Microsoft\Visual Studio 2017\Professional\VC\Auxiliary\Build\vcvarsx86_amd64.bat'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>x86/<address-model>64', setup: 'C:\Programs\Dev\Microsoft\Visual Studio 2017\Professional\VC\Auxiliary\Build\vcvarsx86_amd64.bat'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>ia64/<address-model>', setup: 'C:/Programs/Dev/Microsoft/Visual Studio 2017/Professional/VC/Auxiliary/Build/vcvarsall.bat'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>ia64/<address-model>64', setup: 'C:/Programs/Dev/Microsoft/Visual Studio 2017/Professional/VC/Auxiliary/Build/vcvarsall.bat'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>arm/<address-model>', setup: 'C:/Programs/Dev/Microsoft/Visual Studio 2017/Professional/VC/Auxiliary/Build/vcvarsall.bat'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>arm/<address-model>32', setup: 'C:/Programs/Dev/Microsoft/Visual Studio 2017/Professional/VC/Auxiliary/Build/vcvarsall.bat'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>arm/<address-model>', setup: 'C:/Programs/Dev/Microsoft/Visual Studio 2017/Professional/VC/Auxiliary/Build/vcvarsall.bat'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.1/<architecture>arm/<address-model>64', setup: 'C:/Programs/Dev/Microsoft/Visual Studio 2017/Professional/VC/Auxiliary/Build/vcvarsall.bat'  
notice: [zlib] Using pre-installed library  
notice: [zlib] Condition  
notice: [bzip2] Using pre-installed library  
notice: [bzip2] Condition  
notice: [lzma] Using pre-installed library  
notice: [lzma] Condition  
notice: [zstd] Using pre-installed library  
notice: [zstd] Condition  
notice: [python-cfg] Configuring python...  
notice: [python-cfg] Checking interpreter command "python"...  
notice: [python-cfg] running command 'python -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...does not invoke a working interpreter  
notice: [python-cfg] Python headers and libraries not found.  
...found 1 target...  
  
  
The Boost C++ Libraries were successfully built!  
```

---

## Comment 5

> Username: andry81  
> Created at: 2020-05-15 06:31:51 UTC  
> Updated at: 2020-05-15 06:32:03 UTC  
> Url: https://github.com/boostorg/build/issues/606#issuecomment-629055769  

Seems it tries to find the python of a particular version 3.4 and lower which is not installed.  
I've tried to run with `--without-python`, but still had no luck.  
  
```  
notice: [zlib] Using pre-installed library  
notice: [zlib] Condition  
notice: [bzip2] Using pre-installed library  
notice: [bzip2] Condition  
notice: [lzma] Using pre-installed library  
notice: [lzma] Condition  
notice: [zstd] Using pre-installed library  
notice: [zstd] Condition  
...found 1 target...  
  
The Boost C++ Libraries were successfully built!  
```

---

## Comment 6

> Username: pdimov  
> Created at: 2020-09-05 10:15:16 UTC  
> Url: https://github.com/boostorg/build/issues/606#issuecomment-687584744  

This configuration has always been disallowed. See https://github.com/boostorg/boost/blob/6bc335e5bb1799c8a806dcf1acc2829c8cfdf895/Jamroot#L209-L226  
  
I don't know why you aren't getting the warning message.  
  
Maybe we need an option to override that.

---

## Comment 7

> Username: pdimov  
> Created at: 2020-09-05 15:08:47 UTC  
> Url: https://github.com/boostorg/build/issues/606#issuecomment-687623533  

See https://github.com/boostorg/boost/commit/c5afdca956fc8139a55874c327a802a99df97500. This issues a warning message that the configuration was skipped in the above scenario, and adds support for `--allow-shared-static` that overrides it.

---

## Comment 8

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:53 UTC  
> Url: https://github.com/boostorg/build/issues/606#issuecomment-850868226  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
