# #16 - boost 1.71.0 can't be compiled with mingw [Open]

> Username: Haffon  
> Created at: 2019-08-20 07:00:46 UTC  
> Updated at: 2022-02-07 23:03:35 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16  

bootstrap.bat gcc #come from E:\MinGW\bin  
Building Boost.Build engine  
builtins.cpp: In function 'FILE* windows_popen_wrapper(const char*, const char*)':  
builtins.cpp:2483:39: error: '_popen' was not declared in this scope  
         result = _popen( command, "r" );  
                                       ^  
builtins.cpp: In function 'LIST* builtin_shell(FRAME*, int)':  
builtins.cpp:2549:29: error: '_pclose' was not declared in this scope  
     exit_status = pclose( p );  
...  
  
1.70.0 is okay.

---

## Comment 1

> Username: Haffon  
> Created at: 2019-08-20 13:14:37 UTC  
> Updated at: 2019-08-22 02:08:38 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-523008908  

type erasure module build failed:  
clang-linux.compile.c++.without-pch bin.v2\libs\type_erasure\build\clng-lnx-andrd\rls\lnk-sttc\trgt-os-andrd\thrdp-pthrd\thrd-mlt\vsblt-hdn\dynamic_binding.o  
clang-linux.archive bin.v2\libs\type_erasure\build\clng-lnx-andrd\rls\lnk-sttc\trgt-os-andrd\thrdp-pthrd\thrd-mlt\vsblt-hdn\libboost_type_erasure-clang-mt-a32-1_71.a  
ar: bin.v2\libs\type_erasure\build\clng-lnx-andrd\rls\lnk-sttc\trgt-os-andrd\thrdp-pthrd\thrd-mlt\vsblt-hdn\libboost_type_erasure-clang-mt-a32-1_71.a: No such file or directory  
ranlib: 'bin.v2\libs\type_erasure\build\clng-lnx-andrd\rls\lnk-sttc\trgt-os-andrd\thrdp-pthrd\thrd-mlt\vsblt-hdn\libboost_type_erasure-clang-mt-a32-1_71.a': No such file  
  
    "ar"  rc "bin.v2\libs\type_erasure\build\clng-lnx-andrd\rls\lnk-sttc\trgt-os-andrd\thrdp-pthrd\thrd-mlt\vsblt-hdn\libboost_type_erasure-clang-mt-a32-1_71.a" "bin.v2\libs\type_erasure\build\clng-lnx-andrd\rls\lnk-sttc\trgt-os-andrd\thrdp-pthrd\thrd-mlt\vsblt-hdn\dynamic_binding.o"  
    "ranlib" "bin.v2\libs\type_erasure\build\clng-lnx-andrd\rls\lnk-sttc\trgt-os-andrd\thrdp-pthrd\thrd-mlt\vsblt-hdn\libboost_type_erasure-clang-mt-a32-1_71.a"  
  
...failed clang-linux.archive bin.v2\libs\type_erasure\build\clng-lnx-andrd\rls\lnk-sttc\trgt-os-andrd\thrdp-pthrd\thrd-mlt\vsblt-hdn\libboost_type_erasure-clang-mt-a32-1_71.a...  
...skipped <pC:\Users\01372525\.conan\data\boost\1.71.0\bashbug\stable\build\4e65a98fd5c197b6539ce5c3062c84123c059f0c\boost_1_71_0\armv7\lib>libboost_type_erasure-clang-mt-a32-1_71.a for lack of <pbin.v2\libs\type_erasure\build\clng-lnx-andrd\rls\lnk-sttc\trgt-os-andrd\thrdp-pthrd\thrd-mlt\vsblt-hdn>libboost_type_erasure-clang-mt-a32-1_71.a...  
  
other module or version 1.70.0 is okay. type_erasure issue only occur inside conan, it is okay when build standalone without conan.

---

## Comment 2

> Username: skillcoder  
> Created at: 2019-09-21 17:36:48 UTC  
> Updated at: 2019-09-21 17:41:44 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-533816327  

The same issue for me  
```  
g:\boost_1_71_0>bootstrap.bat gcc  
Building Boost.Build engine  
builtins.cpp: In function 'FILE* windows_popen_wrapper(const char*, const char*)':  
builtins.cpp:2483:39: error: '_popen' was not declared in this scope  
         result = _popen( command, "r" );  
                                       ^  
builtins.cpp: In function 'LIST* builtin_shell(FRAME*, int)':  
builtins.cpp:2549:29: error: '_pclose' was not declared in this scope  
     exit_status = pclose( p );  
                             ^  
debugger.cpp: In function 'char* debug_string_read(FILE*)':  
debugger.cpp:110:33: error: 'strdup' was not declared in this scope  
     result = strdup( buf->value );  
                                 ^  
debugger.cpp: In function 'void debug_init_handles(const char*, const char*)':  
debugger.cpp:1073:43: error: '_fdopen' was not declared in this scope  
     command_input = _fdopen( read_fd, "r" );  
                                           ^  
debugger.cpp: In function 'void init_parent_handles(HANDLE, HANDLE)':  
debugger.cpp:1091:78: error: '_fdopen' was not declared in this scope  
     command_child = _fdopen( _open_osfhandle( (intptr_t)in, _O_RDONLY ), "r" );  
                                                                              ^  
execnt.cpp: In function 'int is_parent_child(DWORD, DWORD)':  
execnt.cpp:1085:61: error: 'stricmp' was not declared in this scope  
                 if ( !stricmp( pinfo.szExeFile, "csrss.exe" ) &&  
                                                             ^  
execnt.cpp:1088:60: error: 'stricmp' was not declared in this scope  
                 if ( !stricmp( pinfo.szExeFile, "smss.exe" ) &&  
                                                            ^  
jam.cpp: In function 'int main(int, char**, char**)':  
jam.cpp:200:22: error: 'environ' was not declared in this scope  
 # define use_environ environ  
                      ^  
jam.cpp:561:37: note: in expansion of macro 'use_environ'  
         var_defines( root_module(), use_environ, 1 );  
                                     ^~~~~~~~~~~  
jam.cpp: In function 'char* executable_path(const char*)':  
jam.cpp:729:66: error: 'strdup' was not declared in this scope  
     return ( !ret || ret == sizeof( buf ) ) ? NULL : strdup( buf );  
                                                                  ^  
sysinfo.cpp: In function 'unsigned int {anonymous}::std_thread_hardware_concurrency()':  
sysinfo.cpp:93:21: error: 'std::thread' has not been declared  
         return std::thread::hardware_concurrency();  
                     ^~~~~~  
```  
bootstrap.log  
```  
###  
### Using 'gcc' toolset.  
###  
  
g:\boost_1_71_0\tools\build\src\engine>g++ -x c++ -std=c++11 -s -O3 -o b2.exe   -DNDEBUG  builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp execnt.cpp filent.cpp filesys.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam.cpp jambase.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp regexp.cpp rules.cpp scan.cpp search.cpp strings.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp    
```  
g:\boost_1_71_0>g++ --version  
```  
g++ (MinGW.org GCC-6.3.0-1) 6.3.0  
Copyright (C) 2016 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```

---

## Comment 3

> Username: Friteee  
> Created at: 2019-09-28 10:58:49 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-536176551  

Same here. Anyone found a solution to this?

---

## Comment 4

> Username: Friteee  
> Created at: 2019-09-30 12:07:43 UTC  
> Updated at: 2019-09-30 12:07:56 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-536531991  

Installed the 1_70 version of boost + installed mingw_64 instead of unmaintained mingw, now compiles smoothly.

---

## Comment 5

> Username: Empischon  
> Created at: 2019-10-10 16:13:17 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-540660514  

Same problem:  
  
D:\CppLib\boost_1_71_0\tools\build>bootstrap.bat mingw  
Bootstrapping the build engine  
builtins.cpp: In function 'FILE* windows_popen_wrapper(const char*, const char*)':  
builtins.cpp:2483:18: error: '_popen' was not declared in this scope  
         result = _popen( command, "r" );  
                  ^~~~~~  
builtins.cpp:2483:18: note: suggested alternative: '_lopen'  
         result = _popen( command, "r" );  
                  ^~~~~~  
                  _lopen  
builtins.cpp: In function 'LIST* builtin_shell(FRAME*, int)':  
builtins.cpp:2418:20: error: '_pclose' was not declared in this scope  
     #define pclose _pclose  
                    ^~~~~~~  
builtins.cpp:2549:19: note: in expansion of macro 'pclose'  
     exit_status = pclose( p );  
                   ^~~~~~  
builtins.cpp:2418:20: note: suggested alternative: '_lclose'  
     #define pclose _pclose  
                    ^~~~~~~  
builtins.cpp:2549:19: note: in expansion of macro 'pclose'  
     exit_status = pclose( p );  
                   ^~~~~~  
debugger.cpp: In function 'char* debug_string_read(FILE*)':  
debugger.cpp:110:14: error: 'strdup' was not declared in this scope  
     result = strdup( buf->value );  
              ^~~~~~  
debugger.cpp:110:14: note: suggested alternative: 'strcmp'  
     result = strdup( buf->value );  
              ^~~~~~  
              strcmp  
debugger.cpp: In function 'void debug_init_handles(const char*, const char*)':  
debugger.cpp:1073:21: error: '_fdopen' was not declared in this scope  
     command_input = _fdopen( read_fd, "r" );  
                     ^~~~~~~  
debugger.cpp:1073:21: note: suggested alternative: '_wsopen'  
     command_input = _fdopen( read_fd, "r" );  
                     ^~~~~~~  
                     _wsopen  
debugger.cpp: In function 'void init_parent_handles(HANDLE, HANDLE)':  
debugger.cpp:1091:21: error: '_fdopen' was not declared in this scope  
     command_child = _fdopen( _open_osfhandle( (intptr_t)in, _O_RDONLY ), "r" );  
                     ^~~~~~~  
debugger.cpp:1091:21: note: suggested alternative: '_wsopen'  
     command_child = _fdopen( _open_osfhandle( (intptr_t)in, _O_RDONLY ), "r" );  
                     ^~~~~~~  
                     _wsopen  
execnt.cpp: In function 'int is_parent_child(DWORD, DWORD)':  
execnt.cpp:1085:23: error: 'stricmp' was not declared in this scope  
                 if ( !stricmp( pinfo.szExeFile, "csrss.exe" ) &&  
                       ^~~~~~~  
execnt.cpp:1085:23: note: suggested alternative: 'strncmp'  
                 if ( !stricmp( pinfo.szExeFile, "csrss.exe" ) &&  
                       ^~~~~~~  
                       strncmp  
execnt.cpp:1088:23: error: 'stricmp' was not declared in this scope  
                 if ( !stricmp( pinfo.szExeFile, "smss.exe" ) &&  
                       ^~~~~~~  
execnt.cpp:1088:23: note: suggested alternative: 'strncmp'  
                 if ( !stricmp( pinfo.szExeFile, "smss.exe" ) &&  
                       ^~~~~~~  
                       strncmp  
jam.cpp: In function 'int main(int, char**, char**)':  
jam.cpp:200:22: error: 'environ' was not declared in this scope  
 # define use_environ environ  
                      ^~~~~~~  
jam.cpp:561:37: note: in expansion of macro 'use_environ'  
         var_defines( root_module(), use_environ, 1 );  
                                     ^~~~~~~~~~~  
jam.cpp:200:22: note: suggested alternative: 'union'  
 # define use_environ environ  
                      ^~~~~~~  
jam.cpp:561:37: note: in expansion of macro 'use_environ'  
         var_defines( root_module(), use_environ, 1 );  
                                     ^~~~~~~~~~~  
jam.cpp: In function 'char* executable_path(const char*)':  
jam.cpp:729:54: error: 'strdup' was not declared in this scope  
     return ( !ret || ret == sizeof( buf ) ) ? NULL : strdup( buf );  
                                                      ^~~~~~  
jam.cpp:729:54: note: suggested alternative: 'strcmp'  
     return ( !ret || ret == sizeof( buf ) ) ? NULL : strdup( buf );  
                                                      ^~~~~~  
                                                      strcmp  
sysinfo.cpp: In function 'unsigned int {anonymous}::std_thread_hardware_concurrency()':  
sysinfo.cpp:93:21: error: 'std::thread' has not been declared  
         return std::thread::hardware_concurrency();  
                     ^~~~~~  
File Not Found  
  
Failed to bootstrap the build engine  
Please consult bootstrap.log for further diagnostics.  
  
D:\CppLib\boost_1_71_0\tools\build>g++ --version  
g++ (MinGW.org GCC-8.2.0-3) 8.2.0  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

---

## Comment 6

> Username: antonypace  
> Created at: 2019-11-15 15:44:51 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-554411376  

Same here - 3 months later. I'm surprised such a fundamental problem got through the release procedure. I'm also going to try reverting to 1.70

---

## Comment 7

> Username: Lusica1031  
> Created at: 2019-12-13 08:15:58 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-565345769  

> Installed the 1_70 version of boost + installed mingw_64 instead of unmaintained mingw, now compiles smoothly.  
  
Thanks! it works for me to change the version.

---

## Comment 8

> Username: gdias1992  
> Created at: 2019-12-16 22:49:10 UTC  
> Updated at: 2019-12-16 22:49:22 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-566282761  

Same problem.  
  
> Same here - 3 months later. I'm surprised such a fundamental problem got through the release procedure. I'm also going to try reverting to 1.70  
  
Thanks!

---

## Comment 9

> Username: changshen  
> Created at: 2020-03-03 20:09:37 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-594144403  

The problem still there.  We must use MinGW_64 g++ compiler?  
  
But there are some issues with MinGW_64/Windows 10/Code::Blocks  
  
g++ --version  
g++ (MinGW.org GCC-8.2.0-3) 8.2.0  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
git source to most current one (sync with master.  version boost-1.72.0)  
  
Building Boost.Build engine  
builtins.cpp: In function 'FILE* windows_popen_wrapper(const char*, const char*)':  
builtins.cpp:2483:18: error: '_popen' was not declared in this scope  
         result = _popen( command, "r" );  
                  ^~~~~~  
builtins.cpp:2483:18: note: suggested alternative: '_lopen'  
         result = _popen( command, "r" );  
                  ^~~~~~  
                  _lopen  
builtins.cpp: In function 'LIST* builtin_shell(FRAME*, int)':  
builtins.cpp:2418:20: error: '_pclose' was not declared in this scope  
     #define pclose _pclose  
                    ^~~~~~~  
builtins.cpp:2549:19: note: in expansion of macro 'pclose'  
     exit_status = pclose( p );  
                   ^~~~~~  
builtins.cpp:2418:20: note: suggested alternative: '_lclose'  
     #define pclose _pclose  
                    ^~~~~~~  
builtins.cpp:2549:19: note: in expansion of macro 'pclose'  
     exit_status = pclose( p );  
                   ^~~~~~  
debugger.cpp: In function 'char* debug_string_read(FILE*)':  
debugger.cpp:110:14: error: 'strdup' was not declared in this scope  
     result = strdup( buf->value );  
              ^~~~~~  
debugger.cpp:110:14: note: suggested alternative: 'strcmp'  
     result = strdup( buf->value );  
              ^~~~~~  
              strcmp  
debugger.cpp: In function 'void debug_init_handles(const char*, const char*)':  
debugger.cpp:1073:21: error: '_fdopen' was not declared in this scope  
     command_input = _fdopen( read_fd, "r" );  
                     ^~~~~~~  
debugger.cpp:1073:21: note: suggested alternative: '_wsopen'  
     command_input = _fdopen( read_fd, "r" );  
                     ^~~~~~~  
                     _wsopen  
debugger.cpp: In function 'void init_parent_handles(HANDLE, HANDLE)':  
debugger.cpp:1091:21: error: '_fdopen' was not declared in this scope  
     command_child = _fdopen( _open_osfhandle( (intptr_t)in, _O_RDONLY ), "r" );  
                     ^~~~~~~  
debugger.cpp:1091:21: note: suggested alternative: '_wsopen'  
     command_child = _fdopen( _open_osfhandle( (intptr_t)in, _O_RDONLY ), "r" );  
                     ^~~~~~~  
                     _wsopen  
execnt.cpp: In function 'int is_parent_child(DWORD, DWORD)':  
execnt.cpp:1085:23: error: 'stricmp' was not declared in this scope  
                 if ( !stricmp( pinfo.szExeFile, "csrss.exe" ) &&  
                       ^~~~~~~  
execnt.cpp:1085:23: note: suggested alternative: 'strncmp'  
                 if ( !stricmp( pinfo.szExeFile, "csrss.exe" ) &&  
                       ^~~~~~~  
                       strncmp  
execnt.cpp:1088:23: error: 'stricmp' was not declared in this scope  
                 if ( !stricmp( pinfo.szExeFile, "smss.exe" ) &&  
                       ^~~~~~~  
execnt.cpp:1088:23: note: suggested alternative: 'strncmp'  
                 if ( !stricmp( pinfo.szExeFile, "smss.exe" ) &&  
                       ^~~~~~~  
                       strncmp  
jam.cpp: In function 'int main(int, char**, char**)':  
jam.cpp:200:22: error: 'environ' was not declared in this scope  
 # define use_environ environ  
                      ^~~~~~~  
jam.cpp:561:37: note: in expansion of macro 'use_environ'  
         var_defines( root_module(), use_environ, 1 );  
                                     ^~~~~~~~~~~  
jam.cpp:200:22: note: suggested alternative: 'union'  
 # define use_environ environ  
                      ^~~~~~~  
jam.cpp:561:37: note: in expansion of macro 'use_environ'  
         var_defines( root_module(), use_environ, 1 );  
                                     ^~~~~~~~~~~  
jam.cpp: In function 'char* executable_path(const char*)':  
jam.cpp:729:54: error: 'strdup' was not declared in this scope  
     return ( !ret || ret == sizeof( buf ) ) ? NULL : strdup( buf );  
                                                      ^~~~~~  
jam.cpp:729:54: note: suggested alternative: 'strcmp'  
     return ( !ret || ret == sizeof( buf ) ) ? NULL : strdup( buf );  
                                                      ^~~~~~  
                                                      strcmp  
sysinfo.cpp: In function 'unsigned int {anonymous}::std_thread_hardware_concurrency()':  
sysinfo.cpp:93:21: error: 'std::thread' has not been declared  
         return std::thread::hardware_concurrency();  
                     ^~~~~~  
File Not Found  
  
Failed to build Boost.Build engine.  
Please consult bootstrap.log for further diagnostics.

---

## Comment 10

> Username: jungletek  
> Created at: 2020-03-03 20:37:51 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-594156337  

If you're posting code, use the code tags FFS.

---

## Comment 11

> Username: morinmorin  
> Created at: 2020-05-15 18:59:59 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-629425610  

@mclow, @grafikrobot, I think this is an issue in Boost.Build rather than Boost.TypeErasure.  
  
`src/engine/builtins.cpp` uses `_popen/_pclose`, but they are not defined when compiled with `-std=c++**` options on MinGW GCC. (Mingw-w64 GCC defines them even with `-std=c++**` options.) Solutions would be  
- Use `-std=gnu++**` option; or  
- Add `-U__STRICT_ANSI__` option.

---

## Comment 12

> Username: wut08  
> Created at: 2020-06-27 19:44:18 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-650611374  

Not just MingW, it can't be compiled with anything. going to try 1.70

---

## Comment 13

> Username: grafikrobot  
> Created at: 2020-06-27 20:10:51 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-650615415  

I don't remember about the B2 that shipped with 1.71.. But at least the current B2 (4.3) builds fine with MinGW..  
  
https://dev.azure.com/grafikrobot/B2/_build/results?buildId=924&view=logs&j=305851a9-a7bb-55db-0042-7e2b6f48aa1c&t=37d4889e-ed03-50ca-1a92-27bcc3794f08&l=13  
  
Some important facts:  
* You can use the most current b2 (of the 4.x series) to build any Boost version back to Boost 1.66.0.  
* If you have another non-mingw compiler you can use that to build B2. The compiler you use to build B2 has no bearing on the compiler you use to build Boost itself.

---

## Comment 14

> Username: boroboro77  
> Created at: 2020-11-30 16:04:07 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-735878714  

I have the same issue.

---

## Comment 15

> Username: doug-lew-diagraph  
> Created at: 2021-01-07 13:17:55 UTC  
> Updated at: 2021-01-07 14:06:00 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-756110686  

I'm currently trying to build boost 1.75, running boostrap.sh, in MinGW with gcc 6.3.0 and I am running into the same error:  
  
 sysinfo.cpp:93:21: error: 'std::thread' has not been declared  
  
I had previously had -std=c++11 but based on the recommendation above I changed it to "gnu++11" but there is no change the build still generates the same error.

---

## Comment 16

> Username: jungletek  
> Created at: 2021-01-07 15:42:07 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-756194907  

> I'm currently trying to build boost 1.75, running boostrap.sh, in MinGW with gcc 6.3.0 and I am running into the same error:  
>   
> sysinfo.cpp:93:21: error: 'std::thread' has not been declared  
  
Pretty sure that's because the version of MinGW is old enough that it doesn't have std::thread in the stdlib. Strongly suggest switching to a maintained version as suggested earlier in the thread. I switched to MSYS2 and the mingw-w64 that is available via its package manager, and am very happy with it (also Boost is available, so really no need to compile it yourself anymore unless you have a special case).

---

## Comment 17

> Username: doug-lew-diagraph  
> Created at: 2021-01-07 18:30:22 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-756297579  

Thank you for your recommendation jungletek!  I upgraded my version on MinGW and used the package manager to get the latest gcc tool set (10.2.0).  I am no longer seeing this error when I execute ./bootstrap.sh.  It still does not build but due to other errors that will be in another post.  
I do need to do the build because my project is cross compiling to an embedded ARM processor.

---

## Comment 18

> Username: eristavi  
> Created at: 2022-02-07 18:47:35 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-1031799580  

I don't know how helpful it will be for anyone but here is my boost successful installation and configuration.   
  
Please note that the following instructions are for MSVC 14.2 without Mingw   
  
1.  Download the binaries from version  [1.78.0](https://sourceforge.net/projects/boost/files/boost-binaries/1.78.0/) or you may choose the version you desire.  
[boost_1_78_0-msvc-14.2-64.exe](https://sourceforge.net/projects/boost/files/boost-binaries/1.78.0/boost_1_78_0-msvc-14.2-64.exe/download)  
[boost_1_78_0-msvc-12.0-32.exe](https://sourceforge.net/projects/boost/files/boost-binaries/1.78.0/boost_1_78_0-msvc-12.0-32.exe/download)  
  
2. Install the downloaded binaries  
In my case, I have created a dev folder in my D drive  `D:/dev` and installed boost there. During the installation Boost installer asked for installation directory default was `C:/Program Files/Boost_1_78_0` I changed the directory as I like things readable and clean `D:/dev/boost`  
  
3. Open the CMD "Command Prompt" and navigate to the installed folder in my case it's `D:/dev/boost`  
_Note: Don't use PowerShell, or PowerShell 7. It gave me some weird errors._  
use the command `bootstrap.bat` to initiate the installer and generate `b2.exe` file  
  
4. ### **Let's Start Building**  
**a. Building for x86 architecture** use the following command  
`b2 --build-dir=build\x86 address-model=32 threading=multi --stagedir=.\bin\x86 --toolset=msvc -j 16 link=static,shared runtime-link=static,shared --variant=debug,release`  
  
**b. Building Debug for x64 architecture** use the following command  
`b2 --build-dir=build\x64 address-model=64 threading=multi --stagedir=.\bin\x64 --toolset=msvc -j 8 link=static,shared runtime-link=static,shared --variant=debug,release`  
  
5. Add the Environment Variables to the Path to generated binaries. In my example is the following  
  
  
**Explaining the build command arguments**  
  
Arguments used in the previous two commands.  
`--build-dir:` Specify the directory to place all the intermediate files while building.  
`address-model:` Specify the targeting address model.  
`threading: `Compile Boost to be thread-aware. (see [this question on stackoverflow](https://stackoverflow.com/questions/20859761/what-exactly-does-threading-multi-do-when-compiling-boost) for more info.)  
`--stage-dir:` the directory where the binaries will be placed.  
`--toolset: `The compiler and linker that will be used to build the artefacts. We chose msvc; this should be the default on Windows, but in case we have more compilers installed on the system, it’s better to explicitly define it.  
`-j: `how many threads to use for building. This can drastically improve the build times on multi-threading environments, i.e. most modern machines.  
`link:` declare a library target.  
`runtime-link: `linking statically/dynamically to the C++ standard library and compiler runtime support libraries.  
`--variant: `Build debug or release version of the binaries.  
  
  
[Source: George Gkasdrogkas](https://levelup.gitconnected.com/the-definite-guide-on-compiling-and-linking-boost-c-libraries-for-visual-studio-projects-c79464d7282d) See the full guide. It's very clean and easy.  
  
  
Regards

---

## Comment 19

> Username: jungletek  
> Created at: 2022-02-07 23:03:34 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/16#issuecomment-1032022491  

> I don't know how helpful it will be for anyone but here is my boost successful installation and configuration.  
>   
> Please note that the following instructions are for MSVC 14.2 without Mingw  
  
The build flags may be helpful for some, but this is a thread about MinGW, so providing MSVC-related stuff is of questionable utility.
