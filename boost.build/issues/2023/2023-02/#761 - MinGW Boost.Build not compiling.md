# #761 - MinGW Boost.Build not compiling [Closed]

> Username: raiseFlaymeException  
> Created at: 2023-02-25 19:28:24 UTC  
> Updated at: 2023-02-26 05:11:33 UTC  
> Closed at: 2023-02-26 05:11:32 UTC  
> Url: https://github.com/boostorg/build/issues/761  

I installed boost 1.81 on my computer and run   
`bootstrap.bat gcc`  
I get the following output:  
`Building Boost.Build engine  
###  
### Using 'gcc' toolset.  
###  
  
C:\install\boost_1_81_0\boost_1_81_0\tools\build\src\engine>"g++" -x c++ -std=c++11 -s -O3 -o b2.exe -D_GNU_SOURCE   -DNDEBUG  builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp execnt.cpp execunix.cpp filent.cpp filesys.cpp fileunix.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp pathunix.cpp regexp.cpp rules.cpp scan.cpp search.cpp jam_strings.cpp startup.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp  
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
 The volume in drive C is called Windows  
 Volume serial number is **********  
  
 Dirrectory C:\install\boost_1_81_0\boost_1_81_0\tools\build\src\engine  
  
 File not found  
  
Failed to build Boost.Build engine.`  
(I tried to translate some output)  
  
Thanks for your help,  
Flayme

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2023-02-26 05:11:31 UTC  
> Url: https://github.com/boostorg/build/issues/761#issuecomment-1445270768  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
