# #40 - Compilation error when using c++20 without char8_t support [Closed]

> Username: d0nc1h0t  
> Created at: 2022-12-21 15:21:30 UTC  
> Updated at: 2023-01-19 01:10:38 UTC  
> Closed at: 2023-01-19 01:10:38 UTC  
> Url: https://github.com/boostorg/static_string/issues/40  

Using the c++20 version of the standard does not automatically support char8_t. Compilers have options to disable various features, including char8_t support (for example, '-fno-char8_t' in GCC). It is more correct to check the support directly for char8_t via __cpp_char8_t.
