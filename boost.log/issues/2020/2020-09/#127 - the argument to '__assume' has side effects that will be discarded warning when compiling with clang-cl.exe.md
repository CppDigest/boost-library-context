# #127 - the argument to '__assume' has side effects that will be discarded warning when compiling with clang-cl.exe [Closed]

> Username: k15tfu  
> Created at: 2020-09-08 09:23:43 UTC  
> Updated at: 2020-09-09 22:12:10 UTC  
> Closed at: 2020-09-08 15:33:38 UTC  
> Url: https://github.com/boostorg/log/issues/127  

Hi!  
  
Usage of BOOST_LOG_ASSUME produces a lot of such warnings on Clang on Windows. Is there anything we can do?  
```  
7>boost/log/detail/attachable_sstream_buf.hpp(181,30): error : the argument to '__assume' has side effects that will be discarded [-Werror,-Wassume]  
```

---

## Comment 1

> Username: k15tfu  
> Created at: 2020-09-09 22:12:10 UTC  
> Url: https://github.com/boostorg/log/issues/127#issuecomment-689849403  

@Lastique and for this one :)
