# #34 - stack-protector warnings should be addressed [Closed]

> Username: BrannonKing  
> Created at: 2019-02-25 23:42:40 UTC  
> Updated at: 2019-03-16 17:07:05 UTC  
> Closed at: 2019-03-16 17:07:05 UTC  
> Url: https://github.com/boostorg/system/issues/34  

I see a lot of this warning when cross-compiling (with a recent build of MinGW):  
```  
/home/me/Workspace/e/bitcoin/depends/x86_64-w64-mingw32/include/boost/system/detail/system_category_win32.hpp:71:21: warning: stack protector not protecting local variables: variable length buffer [-Wstack-protector]  
 inline char const * system_category_message_win32( int ev, char * buffer, std::size_t len ) BOOST_NOEXCEPT  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2019-03-05 15:02:32 UTC  
> Url: https://github.com/boostorg/system/issues/34#issuecomment-469713135  

I've removed the use of `alloca`, so this warning should no longer appear. Currently the fix is in the master and develop branches, and will be in 1.70.
