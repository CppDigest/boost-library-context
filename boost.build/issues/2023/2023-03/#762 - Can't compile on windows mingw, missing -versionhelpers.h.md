# #762 - Can't compile on windows mingw, missing <versionhelpers.h> [Closed]

> Username: raiseFlaymeException  
> Created at: 2023-03-10 19:46:22 UTC  
> Updated at: 2023-03-10 19:48:12 UTC  
> Closed at: 2023-03-10 19:48:11 UTC  
> Url: https://github.com/boostorg/build/issues/762  

I run  
  
`bootstrap.bat mingw`  
  
and I get:  
  
`C:\install\boost_1_81_0\boost_1_81_0>bootstrap.bat mingw  
Building Boost.Build engine  
###  
### Using 'mingw' toolset.  
###  
  
C:\install\boost_1_81_0\boost_1_81_0\tools\build\src\engine>"g++" -x c++ -std=c++11 -s -O3 -o b2.exe   -DNDEBUG  builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp execnt.cpp execunix.cpp filent.cpp filesys.cpp fileunix.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp pathunix.cpp regexp.cpp rules.cpp scan.cpp search.cpp jam_strings.cpp startup.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp  
builtins.cpp: In function 'FILE* windows_popen_wrapper(const char*, const char*)':  
builtins.cpp:2036:39: error: '_popen' was not declared in this scope  
         result = _popen( command, "r" );  
                                       ^  
builtins.cpp: In function 'LIST* builtin_shell(FRAME*, int)':  
builtins.cpp:2102:29: error: '_pclose' was not declared in this scope  
     exit_status = pclose( p );  
                             ^  
debugger.cpp: In function 'void debug_init_handles(const char*, const char*)':  
debugger.cpp:1057:43: error: '_fdopen' was not declared in this scope  
     command_input = _fdopen( read_fd, "r" );  
                                           ^  
debugger.cpp: In function 'void init_parent_handles(HANDLE, HANDLE)':  
debugger.cpp:1073:78: error: '_fdopen' was not declared in this scope  
     command_child = _fdopen( _open_osfhandle( (intptr_t)in, _O_RDONLY ), "r" );  
                                                                              ^  
execnt.cpp:67:28: fatal error: versionhelpers.h: No such file or directory  
 #include <versionhelpers.h>  
                            ^  
compilation terminated.  
jam.cpp: In function 'int guarded_main(int, char**)':  
jam.cpp:185:22: error: 'environ' was not declared in this scope  
 # define use_environ environ  
                      ^  
jam.cpp:488:37: note: in expansion of macro 'use_environ'  
         var_defines( root_module(), use_environ, 1 );  
                                     ^~~~~~~~~~~  
pathsys.cpp: In function 'char* executable_path(const char*)':  
pathsys.cpp:381:66: error: 'strdup' was not declared in this scope  
     return ( !ret || ret == sizeof( buf ) ) ? NULL : strdup( buf );  
                                                                  ^  
  
C:\install\boost_1_81_0\boost_1_81_0\tools\build\src\engine>dir *.exe  
 Le volume dans le lecteur C s’appelle Windows  
 Le numéro de série du volume est 704E-340A  
  
 Répertoire de C:\install\boost_1_81_0\boost_1_81_0\tools\build\src\engine  
  
Fichier introuvable  
  
Failed to build Boost.Build engine.  
  
C:\install\boost_1_81_0\boost_1_81_0>`  
  
(dir *.exe is auto translated to french -> the file is not found)  
  
g++ is on the PATH and here is the ouput of   
  
`g++ -v`  
  
`C:\install\boost_1_81_0\boost_1_81_0>g++ -v  
Using built-in specs.  
COLLECT_GCC=g++  
COLLECT_LTO_WRAPPER=c:/mingw/bin/../libexec/gcc/mingw32/6.3.0/lto-wrapper.exe  
Target: mingw32  
Configured with: ../src/gcc-6.3.0/configure --build=x86_64-pc-linux-gnu --host=mingw32 --with-gmp=/mingw --with-mpfr=/mingw --with-mpc=/mingw --with-isl=/mingw --prefix=/mingw --disable-win32-registry --target=mingw32 --with-arch=i586 --enable-languages=c,c++,objc,obj-c++,fortran,ada --with-pkgversion='MinGW.org GCC-6.3.0-1' --enable-static --enable-shared --enable-threads --with-dwarf2 --disable-sjlj-exceptions --enable-version-specific-runtime-libs --with-libiconv-prefix=/mingw --with-libintl-prefix=/mingw --enable-libstdcxx-debug --with-tune=generic --enable-libgomp --disable-libvtv --enable-nls  
Thread model: win32  
gcc version 6.3.0 (MinGW.org GCC-6.3.0-1)  
  
C:\install\boost_1_81_0\boost_1_81_0>`  
  
Sorry for my english and thank you for the help in advance,  
Flayme

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2023-03-10 19:48:08 UTC  
> Url: https://github.com/boostorg/build/issues/762#issuecomment-1464330519  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
