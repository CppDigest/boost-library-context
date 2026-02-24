# #13 Explicit cast str literal to char * for clang-tidy [Closed]

> Username: tonyelewis  
> Created at: 2019-05-16 02:11:53 UTC  
> Updated at: 2020-12-19 13:57:57 UTC  
> Closed at: 2020-12-19 13:57:57 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/13  

As per [llvm#28480](https://bugs.llvm.org/show_bug.cgi?id=28480), `clang-tidy` currently warns about conversion of string literals to `char *` if the string literal arises from a predefined macro.  
  
And due to [llvm#32239](https://bugs.llvm.org/show_bug.cgi?id=32239), that affects this code even though it's in a system header.  
  
So this change silences the unhelpful warning.

---
