# #180 - Add support for higher precision timestamps [Open]

> Username: Lastique  
> Created at: 2021-03-20 17:50:53 UTC  
> Updated at: 2021-03-20 17:50:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/180  

Switch Boost.Filesystem to use `std::chrono`/Boost.Chrono for various file timestamps. This would be an API breaking change, so there should be an option to revert to `std::time_t` timestamps (to be removed after some transition period). The built library must remain binary compatible with any user-selected representation of timestamps.  
  
https://github.com/boostorg/log/issues/145 is an example where higher precision timestamps would be useful.
