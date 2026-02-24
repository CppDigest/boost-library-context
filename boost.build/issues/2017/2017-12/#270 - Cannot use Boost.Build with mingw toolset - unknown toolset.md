# #270 - Cannot use Boost.Build with mingw toolset - unknown toolset? [Closed]

> Username: jeking3  
> Created at: 2017-12-07 14:47:03 UTC  
> Updated at: 2020-11-30 16:25:13 UTC  
> Closed at: 2019-04-08 11:09:12 UTC  
> Url: https://github.com/boostorg/build/issues/270  

Starting with boos_1_66_0_b1, I then installed MinGW (https://sourceforge.net/projects/mingw/files/Installer/).  I opened a command prompt and modified my path to have C:\MinGW\bin as the first location:  
```  
c:\boost>gcc --version  
gcc (MinGW.org GCC-6.3.0-1) 6.3.0  
Copyright (C) 2016 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
Then I asked for some help:  
```  
c:\boost>bootstrap.bat help  
...  
Please consult bootstrap.log for further diagnostics.  
...  
c:\boost>type bootstrap.log  
###  
### "Unknown toolset: help"  
###  
### You can specify the toolset as the argument, i.e.:  
###     .\build.bat msvc  
###  
### Toolsets supported by this script are: borland, como, gcc,  
###     gcc-nocygwin, intel-win32, metrowerks, mingw, msvc, vc7, vc8,  
###     vc9, vc10, vc11, vc12, vc14, vc141  
###  
### If you have Visual Studio 2017 installed you will need either update  
### the Visual Studio 2017 installer or run from VS 2017 Command Prompt  
### as we where unable to detect your toolset installation.  
###  
```  
  
**Note it says ".\build.bat" when it means ".\bootstrap.bat".**  
Also note that "mingw" is listed as a supported toolset.  
  
```  
c:\boost>bootstrap.bat mingw  
Building Boost.Build engine  
execcmd.c: In function 'onintr':  
execcmd.c:120:5: warning: implicit declaration of function 'out_printf' [-Wimplicit-function-declaration]  
     out_printf( "...interrupted\n" );  
     ^~~~~~~~~~  
execnt.c: In function 'maxline':  
execnt.c:572:12: warning: type defaults to 'int' in declaration of 'result' [-Wimplicit-int]  
     static result;  
            ^~~~~~  
filent.c: In function 'file_archscan':  
filent.c:358:10: warning: implicit declaration of function 'filelist_empty' [-Wimplicit-function-declaration]  
     if ( filelist_empty( archive->members ) )  
          ^~~~~~~~~~~~~~  
filent.c:360:14: warning: implicit declaration of function 'file_collect_archive_content_' [-Wimplicit-function-declaration]  
         if ( file_collect_archive_content_( archive ) < 0 )  
              ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
jam.c: In function 'main':  
jam.c:669:5: warning: implicit declaration of function 'exec_done' [-Wimplicit-function-declaration]  
     exec_done();  
     ^~~~~~~~~  
make.c: In function 'make':  
make.c:105:5: warning: implicit declaration of function 'exec_init' [-Wimplicit-function-declaration]  
     exec_init();  
     ^~~~~~~~~  
make.c:136:13: warning: implicit declaration of function 'out_printf' [-Wimplicit-function-declaration]  
             out_printf( "...found %d target%s...\n", counts->targets,  
             ^~~~~~~~~~  
make.c: In function 'make0':  
make.c:739:13: warning: implicit declaration of function 'out_flush' [-Wimplicit-function-declaration]  
             out_flush();  
             ^~~~~~~~~  
filesys.c: In function 'file_archivescan_impl':  
filesys.c:360:10: warning: implicit declaration of function 'filelist_empty' [-Wimplicit-function-declaration]  
     if ( filelist_empty( archive->members ) )  
          ^~~~~~~~~~~~~~  
modules/path.c: In function 'path_exists':  
modules/path.c:16:12: warning: implicit declaration of function 'file_query' [-Wimplicit-function-declaration]  
     return file_query( list_front( lol_get( frame->args, 0 ) ) ) ?  
            ^~~~~~~~~~  
  
Failed to build Boost.Build engine.  
Please consult bootstrap.log for further diagnostics.  
  
You can try to obtain a prebuilt binary from  
  
   http://sf.net/project/showfiles.php?group_id=7586&package_id=72941  
  
Also, you can file an issue at http://svn.boost.org  
Please attach bootstrap.log in that case.  
```  
  
**Note it says to file an isuse at svn.boost.org - isn't that deprecated?**  
  
```  
c:\boost>type bootstrap.log  
###  
### Using 'mingw' toolset.  
###  
  
c:\boost\tools\build\src\engine>if exist bootstrap rd /S /Q bootstrap  
  
c:\boost\tools\build\src\engine>md bootstrap  
  
c:\boost\tools\build\src\engine>gcc -DNT -o bootstrap\jam0.exe  command.c compile.c constants.c debug.c execcmd.c execnt.c filent.c frames.c function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c parse.c pathnt.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c builtins.c md5.c class.c cwd.c w32_getreg.c native.c modules/set.c modules/path.c modules/regex.c modules/property-set.c modules/sequence.c modules/order.c  
  
c:\boost\tools\build\src\engine>.\bootstrap\jam0 -f build.jam --toolset=mingw "--toolset-root= "  clean  
###  
### Unknown toolset: mingw  
###  
### Known toolsets are: acc, borland, cc, como, clang, darwin, gcc, gcc-nocygwin, intel-darwin, intel-linux, intel-win32, kcc, kylix, metrowerks, mipspro, msvc, qcc, pathscale, pgi, sun, sunpro, tru64cxx, vacpp, xlcpp, vc7, vc8, vc9, vc10, vc11, vc12, vc14, vc141, vmsdecc  
###  
  
c:\boost\tools\build\src\engine>.\bootstrap\jam0 -f build.jam --toolset=mingw "--toolset-root= "  
###  
### Unknown toolset: mingw  
###  
### Known toolsets are: acc, borland, cc, como, clang, darwin, gcc, gcc-nocygwin, intel-darwin, intel-linux, intel-win32, kcc, kylix, metrowerks, mipspro, msvc, qcc, pathscale, pgi, sun, sunpro, tru64cxx, vacpp, xlcpp, vc7, vc8, vc9, vc10, vc11, vc12, vc14, vc141, vmsdecc  
###  
  
c:\boost\tools\build\src\engine>exit /b 1  
```  
  
**When was support dropped for MinGW, and why does the bootstrap indicate it is still valid?**  
  
So I decided to use `bootstrap.bat gcc` which succeeded.  However then when I tried to build something, it looks like a command was not quoted, and fails:  
  
```  
c:\boost\libs\random>..\..\b2 toolset=gcc clean  
'C:\Program' is not recognized as an internal or external command,  
operable program or batch file.  
C:/boost/tools/build/src/tools\gcc.jam:151: in gcc.init from module gcc  
error: toolset gcc initialization:  
error: no command provided, default command 'g++' not found  
error: initialized from  
C:/boost/tools/build/src/build\toolset.jam:43: in toolset.using from module toolset  
C:/boost/tools/build/src\build-system.jam:461: in process-explicit-toolset-requests from module build-system  
C:/boost/tools/build/src\build-system.jam:527: in load from module build-system  
C:\boost\tools\build\src/kernel\modules.jam:295: in import from module modules  
C:\boost\tools\build\src/kernel/bootstrap.jam:139: in boost-build from module  
C:\boost\boost-build.jam:17: in module scope from module  
```

---

## Comment 1

> Username: eldiener  
> Created at: 2017-12-09 17:30:12 UTC  
> Url: https://github.com/boostorg/build/issues/270#issuecomment-350492040  

What does your user-config.jam look like ?  
  
When I use mingw-64/gcc-7.2 on Windows in user-config.jam I have:  
  
using gcc : 7.2 :   
    C:/Utilities/mingw-w64/i686-7.2.0-posix-dwarf-rt_v5-rev0/mingw32/bin/g++ :  
    <cxxflags>-Wno-unused-local-typedefs   
    <cxxflags>-ftrack-macro-expansion=0   
    <cxxflags>-Wno-unused-variable   
    <cxxflags>-D_GLIBCXX_USE_CXX11_ABI=1   ;  
  
You can ignore my particular <cxxflags> but the important point is to let Boost Build know the full path to the g++ executable. I have also found, like you have, that the full path to the g++ executable must be in the Windows PATH else mingw-64/gcc cannot find all the files it needs to compil;e and link successfully.  
  
BTW using mingw-64/gcc is much easier and better than the mingw/gcc. Also I always make sure that there are no spaces in the path where I install mingw-64/gcc. You can install various releases of mingw-64/gcc by downloading at https://sourceforge.net/projects/mingw-w64/files/latest/download.

---

## Comment 2

> Username: jeking3  
> Created at: 2017-12-09 18:33:37 UTC  
> Url: https://github.com/boostorg/build/issues/270#issuecomment-350496515  

It looks like you are using mingw-w64 or msys2, and not the official/original mingw distribution.  I was using the mingw distribution from https://sourceforge.net/projects/mingw/files/Installer.

---

## Comment 3

> Username: eldiener  
> Created at: 2017-12-09 19:16:05 UTC  
> Url: https://github.com/boostorg/build/issues/270#issuecomment-350499073  

I am using mingw-w64, not msys. It is much superior to using the original mingw distribution, supports both 32-bit and 64-bit builds on the latest versions of gcc on Windows. There is nothing "official" about the original mingw distribution. I do have fairly old versions of gcc I can test successfully with hand-made installs of the original mingw distribution, but I heavily prefer mingw-w64 to run the latest versions of gcc on Windows.   
  
But none of this is really apropros. I can see that boostrap.bat does not support 'mingw', but it should work with 'gcc' on Windows. Using gcc on Windows the important thing is to setup user-config.jam properly so that the path to gcc is found by Boost Build and that path must be in the Windows PATH.

---

## Comment 4

> Username: jeking3  
> Created at: 2017-12-09 19:40:36 UTC  
> Url: https://github.com/boostorg/build/issues/270#issuecomment-350500562  

I just found this nugget in the "Building From Source" documentation:  
  
"A note to Cygwin and MinGW users  
  
If you plan to use your tools from the Windows command prompt, you're in the right place. If you plan to build from the Cygwin bash shell, you're actually running on a POSIX platform and should follow the instructions for getting started on Unix variants. Other command shells, such as MinGW's MSYS, are not supported—they may or may not work."  
  
Perhaps we should change this ticket to officially support mingw-w64 for 32-bit-and 64-bit development?

---

## Comment 5

> Username: eldiener  
> Created at: 2017-12-09 19:51:39 UTC  
> Url: https://github.com/boostorg/build/issues/270#issuecomment-350501160  

I believe MingW is understood as either MingW or mingw-w64. But if you want to update the doc to specify above "MinGW/mingw-w64 users", please do so. You will probably need to bring up this change of the doc as a PR or issue elsewhere.

---

## Comment 6

> Username: BadPistol97  
> Created at: 2019-04-01 08:21:45 UTC  
> Updated at: 2019-04-01 08:36:52 UTC  
> Url: https://github.com/boostorg/build/issues/270#issuecomment-478482977  

Try this,  
  
go to this folder : **boost_1_69_0\tools\build\src\engine**  
  
and run the command : **build.bat gcc**  
  
and then go back to the folder: **boost_1_69_0\tools\build**  
  
and run the command: **bootstrap.bat gcc** then run **.\b2 --prefix=DIR install toolset=gcc**

---

## Comment 7

> Username: jeking3  
> Created at: 2019-04-08 11:09:12 UTC  
> Url: https://github.com/boostorg/build/issues/270#issuecomment-480787812  

I believe we can close this.  Since I opened this issue, I've implemented mingw 32-bit and 64-bit builds in the boost-ci project against 17 different boost repositories all on Appveyor.  I agree the original mingw distribution I linked to is practically defunct now.

---

## Comment 8

> Username: Sreepadashastry  
> Created at: 2019-11-20 20:45:36 UTC  
> Url: https://github.com/boostorg/build/issues/270#issuecomment-556372034  

To build boost_1_64_0 with gcc,  
I installed mingw(https://sourceforge.net/projects/mingw/files/Installer/).  
  
```  
C:\Users\Developer\Desktop\armnn-devenv\boost\boost_1_64_0>gcc --version  
gcc (GCC) 7.4.0  
Copyright (C) 2017 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
Now trying to build boost using gcc,  
```  
C:\Users\Developer\Desktop\armnn-devenv\boost\boost_1_64_0>bootstrap.bat gcc  
Building Boost.Build engine  
execcmd.c: In function 'onintr':                                                                                        execcmd.c:120:5: warning: implicit declaration of function 'out_printf'; did you mean 'lol_print'? [-Wimplicit-function-declaration]                                                                                                                 out_printf( "...interrupted\n" );                                                                                       ^~~~~~~~~~                                                                                                              lol_print                                                                                                          execnt.c: In function 'exec_wait':                                                                                      execnt.c:390:13: warning: implicit declaration of function 'unlink'; did you mean 'unix'? [-Wimplicit-function-declaration]                                                                                                                                  unlink( cmdtab[ i ].command_file->value );                                                                              ^~~~~~                                                                                                                  unix                                                                                                       execnt.c: In function 'maxline':  
execnt.c:524:12: warning: type defaults to 'int' in declaration of 'result' [-Wimplicit-int]  
     static result;  
            ^~~~~~  
execnt.c: In function 'is_parent_child':  
execnt.c:1125:23: warning: implicit declaration of function 'stricmp'; did you mean 'strncmp'? [-Wimplicit-function-declaration]  
                 if ( !stricmp( pinfo.szExeFile, "csrss.exe" ) &&  
                       ^~~~~~~  
                       strncmp  
filent.c:49:10: fatal error: direct.h: No such file or directory  
 #include <direct.h>  
          ^~~~~~~~~~  
compilation terminated.  
jam.c: In function 'main':  
jam.c:185:22: error: 'environ' undeclared (first use in this function); did you mean 'arg_environ'?  
 # define use_environ environ  
                      ^  
jam.c:185:22: note: in definition of macro 'use_environ'  
 # define use_environ environ  
                      ^~~~~~~  
jam.c:185:22: note: each undeclared identifier is reported only once for each function it appears in  
 # define use_environ environ  
                      ^  
jam.c:185:22: note: in definition of macro 'use_environ'  
 # define use_environ environ  
                      ^~~~~~~  
make.c: In function 'make':  
make.c:132:13: warning: implicit declaration of function 'out_printf'; did you mean 'lol_print'? [-Wimplicit-function-declaration]  
             out_printf( "...found %d target%s...\n", counts->targets,  
             ^~~~~~~~~~  
             lol_print  
make.c: In function 'make0':  
make.c:735:13: warning: implicit declaration of function 'out_flush'; did you mean 'fflush'? [-Wimplicit-function-declaration]  
             out_flush();  
             ^~~~~~~~~  
             fflush  
filesys.c: In function 'file_archivescan_impl':  
filesys.c:360:10: warning: implicit declaration of function 'filelist_empty'; did you mean 'filelist_next'? [-Wimplicit-function-declaration]  
     if ( filelist_empty( archive->members ) )  
          ^~~~~~~~~~~~~~  
          filelist_next  
builtins.c:66:0: warning: "WIFEXITED" redefined  
 # define WIFEXITED(w)  (((w) & 0XFFFFFF00) == 0)  
  
In file included from /usr/include/cygwin/stdlib.h:13:0,  
                 from /usr/include/stdlib.h:26,  
                 from jam.h:78,  
                 from builtins.c:7:  
/usr/include/cygwin/wait.h:29:0: note: this is the location of the previous definition  
 #define WIFEXITED(_w)  (((_w) & 0xff) == 0)  
  
builtins.c:67:0: warning: "WEXITSTATUS" redefined  
 # define WEXITSTATUS(w)(w)  
  
In file included from /usr/include/cygwin/stdlib.h:13:0,  
                 from /usr/include/stdlib.h:26,  
                 from jam.h:78,  
                 from builtins.c:7:  
/usr/include/cygwin/wait.h:34:0: note: this is the location of the previous definition  
 #define WEXITSTATUS(_w)  (((_w) >> 8) & 0xff)  
  
builtins.c: In function 'builtin_update_now':  
builtins.c:1473:27: warning: implicit declaration of function 'dup' [-Wimplicit-function-declaration]  
         original_stdout = dup( 0 );  
                           ^~~  
builtins.c:1475:9: warning: implicit declaration of function 'dup2'; did you mean 'grp2'? [-Wimplicit-function-declaration]  
         dup2( fd, 0 );  
         ^~~~  
         grp2  
builtins.c:1512:9: warning: implicit declaration of function 'close'; did you mean 'pclose'? [-Wimplicit-function-declaration]  
         close( original_stdout );  
         ^~~~~  
         pclose  
modules/path.c: In function 'path_exists':  
modules/path.c:16:12: warning: implicit declaration of function 'file_query' [-Wimplicit-function-declaration]  
     return file_query( list_front( lol_get( frame->args, 0 ) ) ) ?  
            ^~~~~~~~~~  
  
Failed to build Boost.Build engine.  
Please consult bootstrap.log for further diagnostics.  
  
You can try to obtain a prebuilt binary from  
  
   http://sf.net/project/showfiles.php?group_id=7586&package_id=72941  
  
Also, you can file an issue at http://svn.boost.org  
Please attach bootstrap.log in that case.  
```  
And here is bootstrap.log  
```  
###  
### Using 'gcc' toolset.  
###  
  
C:\Users\Developer\Desktop\armnn-devenv\boost\boost_1_64_0\tools\build\src\engine>if exist bootstrap rd /S /Q bootstrap   
  
C:\Users\Developer\Desktop\armnn-devenv\boost\boost_1_64_0\tools\build\src\engine>md bootstrap   
  
C:\Users\Developer\Desktop\armnn-devenv\boost\boost_1_64_0\tools\build\src\engine>gcc -DNT -o bootstrap\jam0.exe  command.c compile.c constants.c debug.c execcmd.c execnt.c filent.c frames.c function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c parse.c pathnt.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c builtins.c md5.c class.c cwd.c w32_getreg.c native.c modules/set.c modules/path.c modules/regex.c modules/property-set.c modules/sequence.c modules/order.c   
  
C:\Users\Developer\Desktop\armnn-devenv\boost\boost_1_64_0\tools\build\src\engine>exit /b 1   
```  
How to overcome with this issue.

---

## Comment 9

> Username: cjarv  
> Created at: 2020-07-17 20:40:22 UTC  
> Url: https://github.com/boostorg/build/issues/270#issuecomment-660327936  

Hello this issue is still persisting in boost 1.73.0 latest release with MinGW  
**Not sure how to proceed or where this issue is coming from but it looks like in the building process its missing files. Any direction would be greatly appreciated. Thank you!**  
  
**g++ info:**  
```  
C:\Program Files\boost\boost_1_73_0\tools\build>g++ -v  
Using built-in specs.  
COLLECT_GCC=g++  
COLLECT_LTO_WRAPPER=c:/mingw/bin/../libexec/gcc/mingw32/9.2.0/lto-wrapper.exe  
Target: mingw32  
Configured with: ../src/gcc-9.2.0/configure --build=x86_64-pc-linux-gnu --host=mingw32 --target=mingw32 --disable-win32-registry --with-arch=i586 --with-tune=generic --enable-static --enable-shared --enable-threads --enable-languages=c,c++,objc,obj-c++,fortran,ada --with-dwarf2 --disable-sjlj-exceptions --enable-version-specific-runtime-libs --enable-libgomp --disable-libvtv --with-libiconv-prefix=/mingw --with-libintl-prefix=/mingw --enable-libstdcxx-debug --disable-build-format-warnings --prefix=/mingw --with-gmp=/mingw --with-mpfr=/mingw --with-mpc=/mingw --with-isl=/mingw --enable-nls --with-pkgversion='MinGW.org GCC Build-2'  
Thread model: win32  
gcc version 9.2.0 (MinGW.org GCC Build-2)  
```  
  
**Boost.Build bootsrap.bat gcc error**  
```  
Building the B2 engine..  
builtins.cpp: In function 'FILE* windows_popen_wrapper(const char*, const char*)':  
builtins.cpp:2487:18: error: '_popen' was not declared in this scope; did you mean '_lopen'?  
 2487 |         result = _popen( command, "r" );  
      |                  ^~~~~~  
      |                  _lopen  
builtins.cpp: In function 'LIST* builtin_shell(FRAME*, int)':  
builtins.cpp:2422:20: error: '_pclose' was not declared in this scope; did you mean '_lclose'?  
 2422 |     #define pclose _pclose  
      |                    ^~~~~~~  
builtins.cpp:2553:19: note: in expansion of macro 'pclose'  
 2553 |     exit_status = pclose( p );  
      |                   ^~~~~~  
debugger.cpp: In function 'char* debug_string_read(FILE*)':  
debugger.cpp:110:14: error: 'strdup' was not declared in this scope; did you mean 'strcmp'?  
  110 |     result = strdup( buf->value );  
      |              ^~~~~~  
      |              strcmp  
debugger.cpp: In function 'void debug_init_handles(const char*, const char*)':  
debugger.cpp:1046:21: error: '_fdopen' was not declared in this scope; did you mean '_wsopen'?  
 1046 |     command_input = _fdopen( read_fd, "r" );  
      |                     ^~~~~~~  
      |                     _wsopen  
debugger.cpp: In function 'void init_parent_handles(HANDLE, HANDLE)':  
debugger.cpp:1062:21: error: '_fdopen' was not declared in this scope; did you mean '_wsopen'?  
 1062 |     command_child = _fdopen( _open_osfhandle( (intptr_t)in, _O_RDONLY ), "r" );  
      |                     ^~~~~~~  
      |                     _wsopen  
execnt.cpp:58:10: fatal error: versionhelpers.h: No such file or directory  
   58 | #include <versionhelpers.h>  
      |          ^~~~~~~~~~~~~~~~~~  
compilation terminated.  
jam.cpp: In function 'int main(int, char**, char**)':  
jam.cpp:200:22: error: 'environ' was not declared in this scope; did you mean 'union'?  
  200 | # define use_environ environ  
      |                      ^~~~~~~  
jam.cpp:561:37: note: in expansion of macro 'use_environ'  
  561 |         var_defines( root_module(), use_environ, 1 );  
      |                                     ^~~~~~~~~~~  
jam.cpp: In function 'char* executable_path(const char*)':  
jam.cpp:735:54: error: 'strdup' was not declared in this scope; did you mean 'strcmp'?  
  735 |     return ( !ret || ret == sizeof( buf ) ) ? NULL : strdup( buf );  
      |                                                      ^~~~~~  
      |                                                      strcmp  
sysinfo.cpp: In function 'unsigned int {anonymous}::std_thread_hardware_concurrency()':  
sysinfo.cpp:93:21: error: 'std::thread' has not been declared  
   93 |         return std::thread::hardware_concurrency();  
      |                     ^~~~~~  
File Not Found  
  
Failed to build the B2 engine.  
```  
**bootstrap.log**  
```  
###  
### Using 'gcc' toolset.  
###  
  
C:\Program Files\boost\boost_1_73_0\tools\build\src\engine>g++ -x c++ -std=c++11 -s -O3 -o b2.exe   -DNDEBUG  builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp execnt.cpp filent.cpp filesys.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam.cpp jambase.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp regexp.cpp rules.cpp scan.cpp search.cpp jam_strings.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp    
  
C:\Program Files\boost\boost_1_73_0\tools\build\src\engine>dir *.exe   
 Volume in drive C has no label.  
 Volume Serial Number is 2081-865A  
  
 Directory of C:\Program Files\boost\boost_1_73_0\tools\build\src\engine  
  
  
C:\Program Files\boost\boost_1_73_0\tools\build\src\engine>copy /b .\b2.exe .\bjam.exe   
The system cannot find the file specified.  
```

---

## Comment 10

> Username: boroboro77  
> Created at: 2020-11-30 16:25:13 UTC  
> Url: https://github.com/boostorg/build/issues/270#issuecomment-735891598  

I had the same issue. I downloaded boost version 1.70 and it worked.
