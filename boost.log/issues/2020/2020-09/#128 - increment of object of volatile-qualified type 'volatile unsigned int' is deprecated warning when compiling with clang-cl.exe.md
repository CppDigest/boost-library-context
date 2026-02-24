# #128 - increment of object of volatile-qualified type 'volatile unsigned int' is deprecated warning when compiling with clang-cl.exe [Closed]

> Username: k15tfu  
> Created at: 2020-09-08 09:48:07 UTC  
> Updated at: 2020-09-09 22:09:46 UTC  
> Closed at: 2020-09-08 13:08:52 UTC  
> Url: https://github.com/boostorg/log/issues/128  

Hi!  
  
Found one more thing:  
```  
4>boost/log/sinks/basic_sink_frontend.hpp(377,9): error : increment of object of volatile-qualified type 'volatile unsigned int' is deprecated [-Werror,-Wdeprecated-volatile]  
```

---

## Comment 1

> Username: k15tfu  
> Created at: 2020-09-09 22:09:46 UTC  
> Url: https://github.com/boostorg/log/issues/128#issuecomment-689848437  

@Lastique thank you 🎉
