# #671 - Build fails on Mac OS (clang 7.3.0) [Closed]

> Username: Lastique  
> Created at: 2020-11-19 20:53:32 UTC  
> Updated at: 2020-11-22 14:49:20 UTC  
> Closed at: 2020-11-22 14:49:20 UTC  
> Url: https://github.com/boostorg/build/issues/671  

Building Boost.Build on Mac OS Travis CI job [fails](https://travis-ci.org/github/boostorg/core/jobs/743647017):  
  
```  
$ ./bootstrap.sh  
  
Building B2 engine..  
  
###  
  
###  
  
### Using 'clang' toolset.  
  
###  
  
###  
  
> clang++ --version  
  
Apple LLVM version 7.3.0 (clang-703.0.31)  
  
Target: x86_64-apple-darwin15.6.0  
  
Thread model: posix  
  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
  
###  
  
###  
  
> clang++ -x c++ -std=c++11 -O3 -s -DNDEBUG builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp filesys.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathsys.cpp regexp.cpp rules.cpp scan.cpp search.cpp jam_strings.cpp startup.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp execunix.cpp fileunix.cpp pathunix.cpp -o b2  
  
startup.cpp:162:9: error: use of undeclared identifier 'free'  
  
        free(b2_exe_path_pchar);  
  
        ^  
  
1 error generated.  
```  
  
Looks like `#include <stdlib.h>` is missing in startup.cpp.
