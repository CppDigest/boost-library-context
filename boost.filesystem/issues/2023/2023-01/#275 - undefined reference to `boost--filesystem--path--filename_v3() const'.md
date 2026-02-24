# #275 - undefined reference to `boost::filesystem::path::filename_v3() const' [Closed]

> Username: 2246sam20  
> Created at: 2023-01-17 11:05:55 UTC  
> Updated at: 2023-01-22 12:55:00 UTC  
> Closed at: 2023-01-22 12:55:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/275  

I am trying to link boost libraries to my application  but m getting this error  
I have successfully compiled boost for my system  and got the .so files after compiling for my system to link to my  project  
Below are the flags and linking options am using  
  
USER_CPP_OPTS = -O2 -Wall -ffunction-sections -fdata-sections -flto -fvisibility=hidden -fvisibility-inlines-hidden -std+=c++11  
USER_LINK_OPTS = -pie -Wl,-z,relro -Wl,--verbose -lboost_thread -lboost_graph -lboost_filesystem -lboost_system   
```  
undefined reference to `boost::filesystem::path::filename_v3() const'  
 undefined reference to `boost::filesystem::detail::create_directory(boost::filesystem::path const&, boost::filesystem::path const*, boost::system::error_code*)'  
```  
Any help will be appreciated.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-01-22 12:55:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/275#issuecomment-1399477280  

`path::filename_v3` is [present](https://github.com/boostorg/filesystem/blob/9a49c4f3f4624d445dad875ee3d72f2d364c5e39/src/path.cpp#L434) in the library, so my guess is that you're linking against libraries from a different Boost version. Are you missing the `-L` link option?
