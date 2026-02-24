# #26 Support filesystem::path in fstream [Merged]

> Username: Flamefire  
> Created at: 2017-09-29 22:01:32 UTC  
> Updated at: 2020-02-05 16:23:51 UTC  
> Merged at: 2020-02-05 16:19:55 UTC  
> Closed at: 2020-02-05 16:19:55 UTC  
> Url: https://github.com/boostorg/nowide/pull/26  

This adds wchar_t overloads to fstream and filebuf which allows better integration with {boost,std}::filesystem::path and is mandated by C++17 on Windows.  
  
This is then used to accept *::filesystem::path parameters for fstream classes.  
  
Fixes  #23

---
