# #577 - warning: type ‘struct _stack’ violates the C++ One Definition Rule [-Wodr] [Open]

> Username: Kojoley  
> Created at: 2020-04-24 16:14:20 UTC  
> Updated at: 2021-05-29 18:22:17 UTC  
> Url: https://github.com/boostorg/build/issues/577  

Building B2 with `-flto` uncovers a bug:  
```  
> g++ -x c++ -std=c++11 -O2 -s -flto -DNDEBUG builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp filesys.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam.cpp jambase.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathsys.cpp regexp.cpp rules.cpp scan.cpp search.cpp jam_strings.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp execunix.cpp fileunix.cpp pathunix.cpp -o b2  
function.cpp:220:8: warning: type ‘struct _stack’ violates the C++ One Definition Rule [-Wodr]  
 struct _stack  
        ^  
make1.cpp:104:16: note: a different type is defined in another translation unit  
 typedef struct _stack  
                ^  
function.cpp:222:12: note: the first difference of corresponding definitions is field ‘data’  
     void * data;  
            ^  
make1.cpp:106:13: note: a field with different name is defined in another translation unit  
     state * stack;  
             ^  
```

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:21:47 UTC  
> Url: https://github.com/boostorg/build/issues/577#issuecomment-850877266  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
