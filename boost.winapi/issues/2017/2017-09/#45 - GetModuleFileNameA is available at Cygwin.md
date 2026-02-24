# #45 - GetModuleFileNameA is available at Cygwin [Closed]

> Username: apolukhin  
> Created at: 2017-09-06 19:20:14 UTC  
> Updated at: 2017-09-06 19:36:10 UTC  
> Closed at: 2017-09-06 19:33:18 UTC  
> Url: https://github.com/boostorg/winapi/issues/45  

However users complain, that it is not available from Boost.WinAPI:  
  
Phil Bouchard:  
> - I had to change the scope of GetModuleFileNameA in:  
> if (!GetModuleFileNameA(handle, file_name_, DEFAULT_PATH_SIZE_))  
  
Currently that line looks like this https://github.com/boostorg/stacktrace/blob/1614e8ff7d79c091bc9216f4f138670db3db8a33/include/boost/stacktrace/detail/location_from_symbol.hpp#L58 , so Phil dropped the `boost::detail::winapi::` to use the global function

---

## Comment 1

> Username: Lastique  
> Created at: 2017-09-06 19:33:18 UTC  
> Url: https://github.com/boostorg/winapi/issues/45#issuecomment-327589982  

You can see in https://github.com/boostorg/winapi/blob/develop/include/boost/detail/winapi/dll.hpp#L145 that `GetModuleFileNameA` is provided but conditioned on `BOOST_NO_ANSI_APIS`, which is a Boost.Config macro defined on some Windows versions. I don't see how it could have been defined for Cygwin, other that it was defined by user or some code, but I think that's the problem.  
  
In general though, you should either check for `BOOST_NO_ANSI_APIS` as well and use *A or *W functions accordingly or just always use *W functions. I think there are a few exceptions, but most of the Windows API is universally available in *W variant.

---

## Comment 2

> Username: Lastique  
> Created at: 2017-09-06 19:36:10 UTC  
> Url: https://github.com/boostorg/winapi/issues/45#issuecomment-327590641  

Also, make sure you included `boost/detail/winapi/dll.hpp`.
