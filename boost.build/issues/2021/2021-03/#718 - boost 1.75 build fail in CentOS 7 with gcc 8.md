# #718 - boost 1.75 build fail in CentOS 7 with gcc 8. [Open]

> Username: ghost  
> Created at: 2021-03-27 06:09:17 UTC  
> Updated at: 2021-05-29 16:22:52 UTC  
> Url: https://github.com/boostorg/build/issues/718  

I'm trying to install boost 1.75 on CentOS 7, but I can't build b2.  
  
```  
./bootstrap.sh --prefix=/usr --with-python=python3   
Building B2 engine..  
  
###  
###  
### Using 'gcc' toolset.  
###  
###  
  
> g++ --version  
g++ (GCC) 8.3.1 20190311 (Red Hat 8.3.1-3)  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
  
###  
###  
  
> g++ -x c++ -std=c++11 -O2 -s -DNDEBUG builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp filesys.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathsys.cpp regexp.cpp rules.cpp scan.cpp search.cpp jam_strings.cpp startup.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp execunix.cpp fileunix.cpp pathunix.cpp -o b2  
function.cpp: In function ‘void parse_var_string(const char*, const char*, dynamic_array*)’:  
function.cpp:2216:17: error: ‘else’ without a previous ‘if’  
                 else  
                 ^~~~  
  
Failed to build B2 build engine  
```  
  
What I still lack for me to be able to do that?  Thank you!  
  
PS: Is there a possibility that I can do this using the clang? I have version 11 installed.

---

## Comment 1

> Username: ghost  
> Created at: 2021-03-28 20:22:11 UTC  
> Url: https://github.com/boostorg/build/issues/718#issuecomment-808953695  

I discovered the problem accidentally, after removing the LLVM. In short, I can't compile the boost if I also have LLVM installed. I don't this understand why it happened and if it only happened to me, but it is a very uncomfortable problem, because sooner or later, I may need the LLVM again.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:22 UTC  
> Url: https://github.com/boostorg/build/issues/718#issuecomment-850859590  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
