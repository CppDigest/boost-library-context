# #251 Fix crashes in Windows if mixing Debug/Release with interprocess synchronization primitives [Open]

> Username: MikkiWells  
> Created at: 2025-02-12 17:16:59 UTC  
> Updated at: 2025-03-11 17:28:09 UTC  
> Url: https://github.com/boostorg/interprocess/pull/251  

…a fixed binary format  
  
The windows_bootstamp clasa uses a simple C style string so that the binary format is the same in debug and release builds  
  
There can be multiple modules which use boost interprocess named objects (e.g. named_recursive_mutex). These modules can be built in release or debug mode and hence the binary format of std::basic_string<> will differ. This results in an access violation if the windows_intermodule_singleton for the windows_bootstamp<CharT> is created in a module built in release mode, and accessed in a module build in debug mode (or vice-versa). It is not only release versus debug that is an issue. The access violation could also occur if the modules are built with different compilers.  
  
The solution is to create a new windows_intermodule_singleton because we cannot change the format of the existing windows_bootstamp<CharT> class. The new singleton uses only C functions to allocate and free the the character string. This ensures that the build configuration mode or the compiler do not affect the binary layout of the singleton object.  
  
The new windows_intermodule_singleton has a class name which includes a version number. E.g. windows_bootstamp::version1  
  
If the layout of this class needs to change, then the version number must be incremented. This would then create a new singleton, which is required.  
  
This is likely to resolve #94

---
