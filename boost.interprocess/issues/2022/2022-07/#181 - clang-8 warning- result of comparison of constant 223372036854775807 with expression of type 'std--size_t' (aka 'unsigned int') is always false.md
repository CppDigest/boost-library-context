# #181 - clang-8 warning: result of comparison of constant 223372036854775807 with expression of type 'std::size_t' (aka 'unsigned int') is always false [Closed]

> Username: k15tfu  
> Created at: 2022-07-04 15:32:13 UTC  
> Updated at: 2022-07-16 20:29:57 UTC  
> Closed at: 2022-07-16 20:28:56 UTC  
> Url: https://github.com/boostorg/interprocess/issues/181  

Hi!  
  
In file boost/interprocess/detail/os_file_functions.hpp:  
```  
boost/interprocess/detail/os_file_functions.hpp:592:51: error: result of comparison of constant 9223372036854775807 with expression of type  
      'std::size_t' (aka 'unsigned int') is always false [-Werror,-Wtautological-constant-out-of-range-compare]  
   if(uoff_t((std::numeric_limits<off_t>::max)()) < size){  
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ ^ ~~~~  
1 error generated.  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-07-16 20:29:57 UTC  
> Url: https://github.com/boostorg/interprocess/issues/181#issuecomment-1186285003  

Let's hope fixes your case, I have no access to the code the provokes the warning...
