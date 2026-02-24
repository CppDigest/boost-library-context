# #225 - FileSystem version 1.78 causes C4714 warning when compiled with Visual Studio 2015. [Closed]

> Username: dynarithmic  
> Created at: 2022-01-30 02:26:27 UTC  
> Updated at: 2022-01-30 12:32:59 UTC  
> Closed at: 2022-01-30 12:32:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/225  

Boost.FileSystem, boost 1.78  
Compiler: Visual Studio 2015, Update 3.  
  
Using the following and include <boost/filesystem>, the following warning is produced:  
  
`warning C4714: function 'boost::filesystem::path boost::filesystem::path::filename(void) const' marked as __forceinline not inlined`  
  
The error stems from these lines in `boost/filesysystem/path.hpp`:  
  
`BOOST_FORCEINLINE path filename() const { return BOOST_FILESYSTEM_VERSIONED_SYM(filename)(); }   // returns 0 or 1 element path  
BOOST_FORCEINLINE path stem() const { return BOOST_FILESYSTEM_VERSIONED_SYM(stem)(); }           // returns 0 or 1 element path  
BOOST_FORCEINLINE path extension() const { return BOOST_FILESYSTEM_VERSIONED_SYM(extension)(); } // returns 0 or 1 element path`  
  
I don't have an earlier version of boost installed, but from what I remember, this warning didn't appear for earlier versions of boost filesystem.    
  
I could turn off the warning, but would rather have this addressed without explicitly inserting `#pragma warning (disable : 4714)` throughout the codebase.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-01-30 12:32:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/225#issuecomment-1025133859  

Duplicates https://github.com/boostorg/filesystem/issues/212.
