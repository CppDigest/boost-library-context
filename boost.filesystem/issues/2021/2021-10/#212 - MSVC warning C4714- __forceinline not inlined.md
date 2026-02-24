# #212 - MSVC warning  C4714: __forceinline not inlined [Closed]

> Username: Flamefire  
> Created at: 2021-10-26 14:07:14 UTC  
> Updated at: 2021-10-26 16:50:52 UTC  
> Closed at: 2021-10-26 16:50:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/212  

Boost.Filesystem has a more or less breaking change in 1.77 due to https://github.com/boostorg/filesystem/commit/7339882ccfb54293e299af87ee5364e878f5cbb5 introducing BOOST_FORCEINLINE and MSVC helpfully spitting out  
  
>    warning C4714: function 'boost::filesystem::path boost::filesystem::path::filename(void) const' marked as __forceinline not inlined
