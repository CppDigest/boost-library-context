# #387 - Forced include feature [Closed]

> Username: Kojoley  
> Created at: 2019-01-20 01:57:39 UTC  
> Updated at: 2020-04-02 16:56:48 UTC  
> Closed at: 2020-04-02 16:56:48 UTC  
> Url: https://github.com/boostorg/build/issues/387  

I did not find forced include feature which I wish to use with PCH (adding PCH with automatic including the header without spoiling out `#include` in every file). All major compilers support it:  
  
Compiler | Flag | Since version  
-------- | ---- | -------------  
MSVC     | [`/FI`](https://docs.microsoft.com/en-us/cpp/build/reference/fi-name-forced-include-file?view=vs-2017) | [VC4 had it](http://gunkies.org/wiki/Visual_C%2B%2B_4.0)  
GCC      | [`-include`](https://gcc.gnu.org/onlinedocs/gcc/Preprocessor-Options.html#index-include) | [GCC 3.4 had it](https://gcc.gnu.org/onlinedocs/gcc-3.4.0/gcc/Preprocessor-Options.html#Preprocessor-Options)  
Clang    | [`/FI` `-include`](https://clang.llvm.org/docs/UsersManual.html) | [Clang 3.1 had `-include`](https://releases.llvm.org/3.1/tools/clang/docs/UsersManual.html)
