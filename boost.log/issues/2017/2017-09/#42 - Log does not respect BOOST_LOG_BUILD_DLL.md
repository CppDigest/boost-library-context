# #42 - Log does not respect BOOST_LOG_BUILD_DLL [Closed]

> Username: egorpugin  
> Created at: 2017-09-24 23:03:52 UTC  
> Updated at: 2017-09-25 08:42:40 UTC  
> Closed at: 2017-09-25 08:42:40 UTC  
> Url: https://github.com/boostorg/log/issues/42  

Hi,  
  
While all (almost all?) boost libs respect definition `BOOST_ + libname + _BUILD_DLL` when building shared library itself, boost.log uses it own `BOOST_LOG_DLL` definition to say we're compiling a shared library.  
See https://github.com/boostorg/log/blob/develop/include/boost/log/detail/config.hpp#L252 and other occurences of `BOOST_LOG_DLL`.  
  
For reference see boost.thread https://github.com/boostorg/thread/blob/develop/include/boost/thread/detail/config.hpp#L402  
  
Is it possible to fix this to use common boost behavior?  
It will be useful in custom (not b2) boost builds like in my case.

---

## Comment 1

> Username: Lastique  
> Created at: 2017-09-25 08:42:40 UTC  
> Url: https://github.com/boostorg/log/issues/42#issuecomment-331815570  

Sorry, I don't want to do this. There is no common mechanism to indicate that a library is being built as a shared library, every library has its own macro for that. That macro isn't public, it is intended to be used by the build scripts provided by the library. If you want to write your own scripts you will have to adapt it to each library's internals individually (or patch the code). Note however that library internals may change at any time without preserving backward compatibility.
