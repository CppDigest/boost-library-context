# #434 - Unused Variable Warnings Breaks Check Target [Closed]

> Username: ricejasonf  
> Created at: 2019-01-02 00:04:11 UTC  
> Updated at: 2019-01-16 22:27:30 UTC  
> Closed at: 2019-01-02 00:33:47 UTC  
> Url: https://github.com/boostorg/hana/issues/434  

I'm trying to build Hana against my fork of Clang (built with Debug mode), but I'm getting several "unused variable" warnings that halt the build of the `check` target.  
  
I can easily work around this, but the warning seems legit as the variable is in fact not used.  
  
Should these be fixed in Boost.Hana? I see fixes for these in other test files, but I have no idea why it is not triggered in the cases I've found.  
  
```  
Scanning dependencies of target test.tuple.cnstr.variadic_forward  
[  4%] Building CXX object test/CMakeFiles/test.tuple.cnstr.variadic_forward.dir/tuple/cnstr.variadic_forward.cpp.o  
/home/jason/Projects/hana/test/tuple/cnstr.variadic_forward.cpp:54:38: error: unused variable 't0'  
      [-Werror,-Wunused-variable]  
        constexpr hana::tuple<Empty> t0{Empty()};  
                                     ^  
1 error generated.  
  
```  
https://github.com/boostorg/hana/blob/develop/test/tuple/cnstr.variadic_forward.cpp#L54

---

## Comment 1

> Username: ricejasonf  
> Created at: 2019-01-02 00:33:47 UTC  
> Url: https://github.com/boostorg/hana/issues/434#issuecomment-450768554  

It doesn't do this on the `develop` branch. Oh well, I'll just close this. sorry :sweat_smile:

---

## Comment 2

> Username: ricejasonf  
> Created at: 2019-01-16 22:27:30 UTC  
> Url: https://github.com/boostorg/hana/issues/434#issuecomment-454968236  

I think this has something to do with compiling the tests in release mode.
