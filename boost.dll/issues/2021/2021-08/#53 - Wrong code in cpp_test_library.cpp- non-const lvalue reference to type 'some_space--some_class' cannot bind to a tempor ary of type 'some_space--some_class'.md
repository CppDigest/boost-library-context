# #53 - Wrong code in cpp_test_library.cpp: non-const lvalue reference to type 'some_space::some_class' cannot bind to a tempor ary of type 'some_space::some_class' [Closed]

> Username: yuxianch  
> Created at: 2021-08-03 01:41:36 UTC  
> Updated at: 2021-10-01 12:08:34 UTC  
> Closed at: 2021-10-01 12:07:27 UTC  
> Url: https://github.com/boostorg/dll/issues/53  

https://github.com/boostorg/dll/blob/ac134827f348b33dcdc3814b42cff57c2e792aad/test/cpp_test_library.cpp#L93-L102  
  
Compiling this file with "clang-cl -std:c++latest", it will get the following error(the error doesn't show up when using -std:c++20 or older):  
```  
+ clang-cl cpp_test_library.cpp -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs -std:c++latest -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_DEPRECATED '-I..\..\..'  
cpp_test_library.cpp(37,56): warning: volatile-qualified parameter type 'const volatile int' is deprecated [-Wdeprecated-volatile]  
BOOST_SYMBOL_EXPORT void overloaded(const volatile int i)  
                                                       ^  
cpp_test_library.cpp(102,61): error: non-const lvalue reference to type 'some_space::some_class' cannot bind to a temporary of type 'some_space::some_class'  
some_class& some_class::operator=(some_class &&ref) {return ref;}  
                                                            ^~~  
1 warning and 1 error generated.  
+ clang-cl cpp_test_library.cpp -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs -std:c++20 -D_CRT_USE_BUILTIN_OFFSETOF -c -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_NO_DEPRECATED '-I..\..\..'  
cpp_test_library.cpp(37,56): warning: volatile-qualified parameter type 'const volatile int' is deprecated [-Wdeprecated-volatile]  
BOOST_SYMBOL_EXPORT void overloaded(const volatile int i)  
                                                       ^  
1 warning generated.  
```  
In line 102, ref is an rvalue reference (a temporary) and it's being returned as an lvalue reference that cannot have its lifetime extended.   
A workaround is to change the return type to be a const lvalue reference so the lifetime can be extended (or actually honor the move semantics of the parameter).

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-10-01 12:08:34 UTC  
> Url: https://github.com/boostorg/dll/issues/53#issuecomment-932172631  

Many thanks for the issue report. Fixed it in a slightly different way, than was proposed in #55
