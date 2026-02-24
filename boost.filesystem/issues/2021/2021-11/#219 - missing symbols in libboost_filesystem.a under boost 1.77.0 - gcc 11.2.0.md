# #219 - missing symbols in libboost_filesystem.a under boost 1.77.0 / gcc 11.2.0 [Closed]

> Username: MatthewBritton11  
> Created at: 2021-11-26 18:18:30 UTC  
> Updated at: 2021-11-26 19:07:52 UTC  
> Closed at: 2021-11-26 18:54:19 UTC  
> Url: https://github.com/boostorg/filesystem/issues/219  

Compiled boost v1.77 from source under gcc 11.2.0.    
  
Library is missing the symbol boost::filesystem::path::filename()   
  
nm -C libboost_filesystem.a | grep filename  
                 U boost::filesystem::path::remove_filename()  
                 U boost::filesystem::path::remove_filename()  
                 U boost::filesystem::path::filename_v4() const  
0000000000000580 T boost::filesystem::path::remove_filename()  
0000000000000a80 T boost::filesystem::path::filename_v3() const  
0000000000000040 t boost::filesystem::path::filename_v3() const [clone .cold]  
0000000000000dd0 T boost::filesystem::path::filename_v4() const  
0000000000000078 t boost::filesystem::path::filename_v4() const [clone .cold]  
00000000000010f0 T boost::filesystem::path::has_filename_v4() const  
  
This results in link errors of the form   
  
undefined reference to `boost::filesystem::path::filename() const'  
  
Many thanks  
Matthew

---

## Comment 1

> Username: Lastique  
> Created at: 2021-11-26 18:54:19 UTC  
> Url: https://github.com/boostorg/filesystem/issues/219#issuecomment-980296729  

Boost.Filesystem 1.77 defines `path::filename` as [an inline function in the header](https://github.com/boostorg/filesystem/blob/9794725bda2e612f1fdcadd98cdce5b7bfb548f1/include/boost/filesystem/path.hpp#L583). The reason why you may see linking errors is you're using headers that are from an older Boost release (1.76 or older). Possibly, you have not recompiled your code from scratch when you upgraded to Boost 1.77.

---

## Comment 2

> Username: MatthewBritton11  
> Created at: 2021-11-26 19:07:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/219#issuecomment-980313913  

Thank you - dated include files from a previous compile caused the problem.
