# #446 - Duplicate symbol error and DR2387 [Closed]

> Username: vddvss  
> Created at: 2019-04-28 19:10:21 UTC  
> Updated at: 2023-07-22 14:13:58 UTC  
> Closed at: 2019-07-29 17:52:41 UTC  
> Url: https://github.com/boostorg/hana/issues/446  

So I'm not sure if this is a Clang issue or a Hana one, but since https://github.com/llvm/llvm-project/commit/bb750689d51d2109b9898b53a4b69d8183ab1f26, but there are duplicate symbol errors if Hana is included in more than one translation unit. This can be shown with Hana's `test.issues.github_75` (see below).  
  
That commit to LLVM was to address DR2387 for the C++ standard. Unfortunately, I could not find a copy of the DR (I get a 404 from <http://wg21.link/cwg2387>, so I think it will be in the next mailing), but it looks as if it applies the same rules regarding internal linkage of const-qualified variables to const-qualified variable templates.  
  
Because of this, the declaration of `constexpr auto curry_or_call<0>` in `functional/curry.hpp`: <https://github.com/boostorg/hana/blob/9ffeb079fed195849d384fa7ed2f8dbcf9c04c88/include/boost/hana/functional/curry.hpp#L119> looks like it now has external linkage, causing the duplicate symbol error.  Adding `inline` (and ignoring `-Wc++17-extensions`) gets it to work--as does `static` FWIW...  
  
Here is the compiler output from https://github.com/llvm/llvm-project/commit/bb750689d51d2109b9898b53a4b69d8183ab1f26:  
  
```  
$ cmake .. -DCMAKE_CXX_COMPILER=/home/colin/.local/lib64/llvm-installs/bb750689d51/bin/clang++ -DBOOST_ROOT=/home/colin/extra-storage/development/boost-installs/master -G Ninja  
  
$ ninja test.issues.github_75 -v  
[1/3] /home/colin/.local/lib64/llvm-installs/bb750689d51/bin/clang++  -DBOOST_HANA_CONFIG_ENABLE_STRING_UDL -I../include -fdiagnostics-color -ftemplate-backtrace-limit=0 -pedantic -Wall -Wextra -Wno-self-assign-overloaded -Wno-unused-local-typedefs -Wwrite-strings -Wno-gnu-string-literal-operator-template -std=c++14 -MD -MT test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu2.cpp.o -MF test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu2.cpp.o.d -o test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu2.cpp.o -c ../test/issues/github_75/tu2.cpp  
[2/3] /home/colin/.local/lib64/llvm-installs/bb750689d51/bin/clang++  -DBOOST_HANA_CONFIG_ENABLE_STRING_UDL -I../include -fdiagnostics-color -ftemplate-backtrace-limit=0 -pedantic -Wall -Wextra -Wno-self-assign-overloaded -Wno-unused-local-typedefs -Wwrite-strings -Wno-gnu-string-literal-operator-template -std=c++14 -MD -MT test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu1.cpp.o -MF test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu1.cpp.o.d -o test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu1.cpp.o -c ../test/issues/github_75/tu1.cpp  
[3/3] : && /home/colin/.local/lib64/llvm-installs/bb750689d51/bin/clang++     test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu1.cpp.o test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu2.cpp.o  -o test/test.issues.github_75   && :  
FAILED: test/test.issues.github_75  
: && /home/colin/.local/lib64/llvm-installs/bb750689d51/bin/clang++     test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu1.cpp.o test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu2.cpp.o  -o test/test.issues.github_75   && :  
test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu2.cpp.o:(.rodata+0x0): multiple definition of `boost::hana::curry_detail::curry_or_call<0ul>'  
test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu1.cpp.o:(.rodata+0x0): first defined here  
clang-9: error: linker command failed with exit code 1 (use -v to see invocation)  
ninja: build stopped: subcommand failed.  
```  
  
And from the previous commit (https://github.com/llvm/llvm-project/commit/0d923af49270ef4beb5d479dac5bda80fc0082ef):  
  
```  
cmake .. -DCMAKE_CXX_COMPILER=/home/colin/.local/lib64/llvm-installs/0d923af4927/bin/clang++ -DBOOST_ROOT=/home/colin/extra-storage/development/boost-installs/master -G Ninja  
  
$ ninja test.issues.github_75 -v  
[1/3] /home/colin/.local/lib64/llvm-installs/0d923af4927/bin/clang++  -DBOOST_HANA_CONFIG_ENABLE_STRING_UDL -I../include -fdiagnostics-color -ftemplate-backtrace-limit=0 -pedantic -Wall -Wextra -Wno-self-assign-overloaded -Wno-unused-local-typedefs -Wwrite-strings -Wno-gnu-string-literal-operator-template -std=c++14 -MD -MT test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu1.cpp.o -MF test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu1.cpp.o.d -o test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu1.cpp.o -c ../test/issues/github_75/tu1.cpp  
[2/3] /home/colin/.local/lib64/llvm-installs/0d923af4927/bin/clang++  -DBOOST_HANA_CONFIG_ENABLE_STRING_UDL -I../include -fdiagnostics-color -ftemplate-backtrace-limit=0 -pedantic -Wall -Wextra -Wno-self-assign-overloaded -Wno-unused-local-typedefs -Wwrite-strings -Wno-gnu-string-literal-operator-template -std=c++14 -MD -MT test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu2.cpp.o -MF test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu2.cpp.o.d -o test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu2.cpp.o -c ../test/issues/github_75/tu2.cpp  
[3/3] : && /home/colin/.local/lib64/llvm-installs/0d923af4927/bin/clang++     test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu1.cpp.o test/CMakeFiles/test.issues.github_75.dir/issues/github_75/tu2.cpp.o  -o test/test.issues.github_75   && :  
```

---

## Comment 1

> Username: keryell  
> Created at: 2019-07-23 21:36:48 UTC  
> Url: https://github.com/boostorg/hana/issues/446#issuecomment-514393361  

I just hit the same issue... :-(

---

## Comment 2

> Username: keryell  
> Created at: 2019-07-23 22:15:28 UTC  
> Url: https://github.com/boostorg/hana/issues/446#issuecomment-514403780  

And adding C++17 `inline`solved my problem too.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2019-07-23 22:26:36 UTC  
> Updated at: 2019-07-23 22:29:32 UTC  
> Url: https://github.com/boostorg/hana/issues/446#issuecomment-514406564  

Perhaps we could work around this by default constructing an instance of `apply_t`.  
```cpp  
constexpr auto curry_or_call<0> = decltype(apply){};  
```  
  
It doesn't seem correct though. I'm no standards expert.  
  
Is that in a release of Clang?

---

## Comment 4

> Username: keryell  
> Created at: 2019-07-23 22:59:35 UTC  
> Url: https://github.com/boostorg/hana/issues/446#issuecomment-514414055  

I am using ToT Clang/LLVM.  
It is not clear to me which work-around can work with all the compilers from https://github.com/boostorg/hana/wiki/General-notes-on-compiler-support  
@ldionne what do you think?

---

## Comment 5

> Username: ldionne  
> Created at: 2019-07-25 16:25:53 UTC  
> Url: https://github.com/boostorg/hana/issues/446#issuecomment-515112272  

Fix incoming, sorry for the delay!

---

## Comment 6

> Username: keryell  
> Created at: 2019-07-30 00:39:05 UTC  
> Url: https://github.com/boostorg/hana/issues/446#issuecomment-516213619  

@ldionne Great! Thank you.

---

## Comment 7

> Username: catskul  
> Created at: 2023-07-22 03:25:42 UTC  
> Url: https://github.com/boostorg/hana/issues/446#issuecomment-1646442579  

Is there an easy work around for those of use for whom changing hana version or compiler version is not an option?

---

## Comment 8

> Username: ricejasonf  
> Created at: 2023-07-22 03:53:52 UTC  
> Updated at: 2023-07-22 03:58:46 UTC  
> Url: https://github.com/boostorg/hana/issues/446#issuecomment-1646446709  

@catskul   
If you don't use that instance of `curry_or_call` it should not be a problem. Look at the fix: https://github.com/boostorg/hana/commit/7218a046c731b2548b5ae3db55fb711ccda8fbc4  
  
EDIT: Err.. I looked at it and that is in the `detail` namespace. Then we need to know if you can not use `curry` or another implementation of it.

---

## Comment 9

> Username: catskul  
> Created at: 2023-07-22 14:13:58 UTC  
> Url: https://github.com/boostorg/hana/issues/446#issuecomment-1646593212  

I didn't use curry directly but apparently it was used indirectly and still had the same effect.  
  
In my case I was only using `hana::for_each` and `hana::tuple`, but including `hana.hpp`  
  
Now that I think of it I'm guessing the problem is more likely use of `hana.hpp` perhaps if include the minimal header set instead of the catchall header, I can get around it.
