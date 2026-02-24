# #676 - local variable shadows parameter warning [Closed]

> Username: nickhuang99  
> Created at: 2021-05-12 13:12:51 UTC  
> Updated at: 2021-05-17 00:04:03 UTC  
> Closed at: 2021-05-17 00:04:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/676  

https://github.com/boostorg/spirit/blob/32b40e9f82f90c00ebc86884c03c09bf2912009a/include/boost/spirit/home/support/detail/lexer/parser/tokeniser/re_tokeniser_helper.hpp#L526  
  
This local variable shadows function parameter >ch_<  
  
CharT ch_ = static_cast<CharT> (start_);  
  
It is a trivial issue, but it is nice that somebody can rename this local variable.

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-05-12 18:41:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/676#issuecomment-840013298  

Which compiler and warning flag? Spirit is not clear for `-Wshadow`.  
  
I will check what I can do about it, however the last time I tried there were hard to fix local shadowing in macros and namespace global shadowing that requires an API breaking to fix.

---

## Comment 2

> Username: nickhuang99  
> Created at: 2021-05-12 20:04:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/676#issuecomment-840061404  

hi,  
  
First of all, thanks for your prompt response and I really appreciate your wonderful work to boost library which I sincerely hope to do a little bit of my part.  
  
Compiler: gcc-10.2.0  
warning flag: -Wshadow-compatible-local  
boost: I tried 1.72 and see the latest one is the same as 1.72 on this particular issue.  
example code:  word_count_functor.cpp  
  
I use the example code "word_count_functor.cpp" for testing (https://www.boost.org/doc/libs/1_72_0/libs/spirit/example/lex/word_count_functor.cpp)  
  
  
Thank you again,

---

## Comment 3

> Username: nickhuang99  
> Created at: 2021-05-13 14:19:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/676#issuecomment-840593825  

BTW, just FYI, I am using this simple script to build/install my gcc-10.2.0 and hope it may save you some time if you haven't installed gcc-10.2 yet in your system.  
  
https://github.com/nickhuang99/gcc-building/blob/4628d92f4c9c2c751701912f34308b82c27c51c2/build_gcc.sh
