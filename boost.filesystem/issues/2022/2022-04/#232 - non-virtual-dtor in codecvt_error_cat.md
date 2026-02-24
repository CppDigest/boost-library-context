# #232 - [1.74.0] non-virtual-dtor in codecvt_error_cat [Closed]

> Username: Sil3ntStorm  
> Created at: 2022-04-06 15:15:14 UTC  
> Updated at: 2022-04-06 20:03:20 UTC  
> Closed at: 2022-04-06 17:13:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/232  

libs/filesystem/src/codecvt_error_category.cpp:27:9: warning: '(anonymous namespace)::codecvt_error_cat' has virtual functions but non-virtual destructor [-Wnon-virtual-dtor]  
  class codecvt_error_cat : public boost::system::error_category  
        ^

---

## Comment 1

> Username: Lastique  
> Created at: 2022-04-06 20:03:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/232#issuecomment-1090721549  

For the record, the warning was not appearing in the official Boost build. I'm assuming you're building Boost using your own build system that is not equivalent to the official build. You should change your build system behavior to match the official build system.  
  
That said, I silenced the warning anyway, just in case if the warning is manually enabled in the command line.
