# #298 - float_string_cvt.hpp warning: ints different signs on Apple clang [Closed]

> Username: ckormanyos  
> Created at: 2021-02-22 09:19:44 UTC  
> Updated at: 2021-02-23 21:50:18 UTC  
> Closed at: 2021-02-23 21:50:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/298  

Here is an easy one that makes a lot of otherwise very clean compilations suffer from 1 warning.  
  
On Apple clang for various compilers with -Wall, we observe the warning below:  
  
```  
../boost-root/boost/multiprecision/detail/float_string_cvt.hpp:169:26: warning: comparison of integers of different signs: 'std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::size_type' (aka 'unsigned long') and 'std::streamsize' (aka 'long') [-Wsign-compare]  
   while ((result.size() > digits) && result.size())  
           ~~~~~~~~~~~~~ ^ ~~~~~~  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-02-23 21:50:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/298#issuecomment-784536969  

Fixed by #299
