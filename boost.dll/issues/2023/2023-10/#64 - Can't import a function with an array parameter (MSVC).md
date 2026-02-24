# #64 - Can't import a function with an array parameter (MSVC) [Open]

> Username: db4  
> Created at: 2023-10-18 08:00:11 UTC  
> Updated at: 2025-01-28 10:03:47 UTC  
> Url: https://github.com/boostorg/dll/issues/64  

Consider the following example:  
  
```c++  
boostdll::experimental::smart_library lib;  
lib.load("test.dll");  
  
int f(char a[]);  
decltype(f)* pf = lib.get_function<decltype(f)>("f");  
```  
  
This fails to find function `f` as it's demangled as `int __cdecl f(char * __ptr64 const)`, but MSVC parser doesn't accept that trailing `const`
