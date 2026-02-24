# #133 Improve tests [Merged]

> Username: Flamefire  
> Created at: 2021-04-10 11:54:10 UTC  
> Updated at: 2021-04-21 11:17:33 UTC  
> Merged at: 2021-04-21 11:17:28 UTC  
> Closed at: 2021-04-21 11:17:28 UTC  
> Url: https://github.com/boostorg/nowide/pull/133  

- Refactor the tests into more focused units  
- Enhance tests to increase coverage and ensure correctness  
- Remove support code for MSVC < 2012  
- Fix formatting and CI  
  
### Bugs fixed  
  
- Opening a file in `append` mode did not allow writing while it should  
- Performance of utf8_codecvt improved by avoiding double-parsing of UTF-8 chars  
- Close filebuf when swapping as required by the std::filebuf

---
