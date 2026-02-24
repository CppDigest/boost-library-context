# #7 - CI tests for older compilers [Closed]

> Username: anarthal  
> Created at: 2020-11-29 16:54:07 UTC  
> Updated at: 2022-12-10 12:09:16 UTC  
> Closed at: 2022-12-10 12:09:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/7  

The library is designed to support C++11. Make the CIs run the tests for older compiler versions:  
- gcc 4.9 to current  
- clang 3.3 to current  
- Visual Studio 2017 to current  
  
CIs should explicitly use a certain toolchain instead of relying on the platform default, which may change over time.

---

## Comment 1

> Username: anarthal  
> Created at: 2022-12-10 12:09:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/7#issuecomment-1345251180  

Done as of bf4071f37062bd180a495fc73241becb32aa2c6e
