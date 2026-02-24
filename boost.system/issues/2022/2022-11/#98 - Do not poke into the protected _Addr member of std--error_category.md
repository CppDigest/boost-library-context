# #98 - Do not poke into the protected _Addr member of std::error_category [Closed]

> Username: pdimov  
> Created at: 2022-11-08 23:23:01 UTC  
> Updated at: 2022-12-11 00:49:53 UTC  
> Closed at: 2022-12-11 00:49:53 UTC  
> Url: https://github.com/boostorg/system/issues/98  

... as this creates problems for the MS STL (https://github.com/microsoft/STL/pull/3176).  
  
Since a nonzero `id` is only passed when `BOOST_SYSTEM_AVOID_STD_GENERIC_CATEGORY` or `BOOST_SYSTEM_AVOID_STD_SYSTEM_CATEGORY` is defined, and since `id` is only stored into `_Addr` when the MS STL is 14x, but none of these macros are defined in this case, this code should be dead and it should be possible to remove it.
