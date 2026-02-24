# #1848 - Using BoostBeast with OpenMP crashes Clang [Closed]

> Username: planetmarshall  
> Created at: 2020-02-17 15:43:01 UTC  
> Updated at: 2020-02-19 16:00:37 UTC  
> Closed at: 2020-02-19 10:38:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1848  

Clang crashes when compiling a program using Boost.Beast with OpenMP. I realize that this may or may not be a bug with Boost.Build (Clang shouldn't really crash even if the input is invalid). I have also logged a bug with [LLVM Bugzilla](https://bugs.llvm.org/show_bug.cgi?id=44914)  
  
### Version of Beast  
  
1.72.0  
  
### Steps necessary to reproduce the problem  
  
Minimal program provided here -   
  
https://github.com/planetmarshall/boost_beast_openmp  
  
TL;DR, compile a program using `Boost.Beast` with `-fopenmp` and compile it with clang.  
  
### Expected Results:  
  
Something like  
```  
Thread Number: 5  
Thread Number: 0  
Thread Number: 9  
Thread Number: 2  
...etc...  
```  
  
### Actual Results  
  
Clang crashes while generating code for `boost::beast::http::detail::is_token_char` in `boost/beast/http/detail/rfc7230.ipp` [clang_crash.txt](https://github.com/boostorg/beast/files/4214592/clang_crash.txt)  
  
### Workaround  
  
Compiles successfully with GCC 8.3  
  
### All relevant compiler information  
  
* Ubuntu 18.04 LTS  
* Clang 9  
* Boost 1.72.0

---

## Comment 1

> Username: planetmarshall  
> Created at: 2020-02-17 15:46:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1848#issuecomment-587053485  

For the record, I only came across this because my openmp flags were propagating through my codebase due to me not using CMake properly. I don't actually *need* to use OpenMP with Boost Beast, but thought it was worth logging.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-02-17 16:01:29 UTC  
> Updated at: 2020-02-17 16:01:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1848#issuecomment-587060078  

Thanks for logging this @planetmarshall.  
  
Kudos for providing an MCVE!

---

## Comment 3

> Username: planetmarshall  
> Created at: 2020-02-17 16:39:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1848#issuecomment-587076828  

Just tested on Clang 11 and it compiles fine.

---

## Comment 4

> Username: planetmarshall  
> Created at: 2020-02-19 10:38:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1848#issuecomment-588153908  

I'm pretty sure this is a Clang issue and nothing to do with Boost Beast per se, so will close. You can even crash the compiler on Compiler Explorer by adding `-fopenmp` to the compiler options if you are so inclined :) https://godbolt.org/z/yPkWYt

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-02-19 16:00:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1848#issuecomment-588294300  

nice trick
