# #191 - Optional std::filesystem [Open]

> Username: Nekto89  
> Created at: 2022-07-21 12:50:11 UTC  
> Updated at: 2022-07-21 13:18:15 UTC  
> Url: https://github.com/boostorg/log/issues/191  

It would be nice if there would be some magic option that will allow to build boost::log with std::filesystem instead of boost::filesystem. This will help with symbol problems in static configuration caused by BOOST_FILESYSTEM_VERSION define.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-07-21 12:53:49 UTC  
> Url: https://github.com/boostorg/log/issues/191#issuecomment-1191448657  

Could you describe what kind of problems?

---

## Comment 2

> Username: Nekto89  
> Created at: 2022-07-21 13:18:15 UTC  
> Url: https://github.com/boostorg/log/issues/191#issuecomment-1191475110  

Some public methods in boost::filesystem are marked as inline and have different implementation depending on BOOST_FILESYSTEM_VERSION. From Microsoft documentation: "The compiler treats the inline expansion options and keywords as suggestions. There's no guarantee that functions will be inlined. You can't force the compiler to inline a particular function, even with the __forceinline keyword. "  
  
In some rare cases these simple one-liners aren't being inlined. So if boost is used as static library (users of shared libraries are safe :)), it might lead to situations when wrong version of method is linked in final binary. For example, if libboost_filesystem.lib has this "inlined" method with v4 and user's code (.obj/.lib files) expects that v3 will be called.  
  
I've already replaced all boost::filesystem usages from my code, only transitive boost::process and boost::log are left.  
boost::process will have "BOOST_PROCESS_USE_STD_FS" in next version.  
It would be nice if it would be possible to have similar define in boost::log.
