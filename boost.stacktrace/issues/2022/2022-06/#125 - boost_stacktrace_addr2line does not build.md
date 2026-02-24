# #125 - boost_stacktrace_addr2line does not build [Closed]

> Username: lightupmemory  
> Created at: 2022-06-25 05:37:50 UTC  
> Updated at: 2024-09-12 08:27:50 UTC  
> Closed at: 2024-09-12 08:27:50 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/125  

Hello,   
I'm trying to cross build boost for M1 on an Intel mac.  
Everything goes fine, except addr2line does not build.  
From the configuration check, it shows  
```  
    - libbacktrace builds      : no [2]  
    - libbacktrace builds      : no [3]  
    - addr2line builds         : no [2]  
    - addr2line builds         : no [3]  
```  
  
below is my script  
```shell-script  
chmod u+x bootstrap.sh tools/build/src/engine/build.sh  
  
./bootstrap.sh --with-python=/usr/bin/python3  
  
./b2 install architecture=arm \  
"cxxflags=-std=c++17 -arch arm64 -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk -mmacosx-version-min=11.0" \  
"cflags=-arch arm64 -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk -mmacosx-version-min=11.0" \  
"linkflags=-arch arm64 -Wl,-syslibroot,/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk -mmacosx-version-min=11.0" \  
--prefix=prefix abi=aapcs  
````  
  
building environment  
- operating system: macOS 12.0.1  
- Xcode version: 12.4  
  
It seems that macOS doesn't have addr2line at /usr/bin/addr2line. I'm wondering how does boost check for addr2line builds.  
Moreover, building boost natively (either on intel or M1) and cross building for x86_64 on M1 all generate boost_stacktrace_addr2line. Is it the limitation on intel mac for cross building?

---

## Comment 1

> Username: DDoSolitary  
> Created at: 2022-08-07 07:01:17 UTC  
> Updated at: 2022-08-07 07:03:06 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/125#issuecomment-1207344725  

I got a similar problem when cross building for Android. The cause seems to be that the build script detects addr2line and libbacktrace by trying to compile and **run** some C++ code  
  
https://github.com/boostorg/stacktrace/blob/75b7986f9799184ecd679d86273532cb54e6a0dc/build/Jamfile.v2#L42-L52  
  
Unfortunately, as we are cross compiling, the code is built for the target system and won't run on host system.  
  
Error log from config.log:  
  
```  
/bin/sh: 2: <redacted>/boost/bin.v2/libs/stacktrace/build/clng-lnx-7/dbg/lnk-sttc/nm-on/trgt-os-andrd/thrd-mlt/vsblt-hdn/    libbacktrace_exe: Exec format error  
...removing <redacted>/boost/bin.v2/libs/stacktrace/build/clng-lnx-7/dbg/lnk-sttc/nm-on/trgt-os-andrd/thrd-mlt/vsblt-hdn/    libbacktrace.output  
...failed updating 1 target...  
/bin/sh: 2: <redacted>/boost/bin.v2/libs/stacktrace/build/clng-lnx-7/dbg/lnk-sttc/nm-on/trgt-os-andrd/thrd-mlt/vsblt-hdn/    addr2line_exe: Exec format error  
...removing <redacted>/boost/bin.v2/libs/stacktrace/build/clng-lnx-7/dbg/lnk-sttc/nm-on/trgt-os-andrd/thrd-mlt/vsblt-hdn/    addr2line.output  
...failed updating 1 target...  
```

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-09-12 08:27:50 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/125#issuecomment-2345602988  

For non standard addr2line locations there is a macro BOOST_STACKTRACE_ADDR2LINE_LOCATION https://www.boost.org/doc/libs/1_86_0/doc/html/stacktrace/configuration_and_build.html  
  
If that does not help, please find a solution and send a PR, as I have no access to M1 platform.
