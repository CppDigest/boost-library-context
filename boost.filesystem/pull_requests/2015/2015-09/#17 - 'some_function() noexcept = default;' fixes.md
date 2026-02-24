# #17 'some_function() noexcept = default;' fixes [Closed]

> Username: apolukhin  
> Created at: 2015-09-28 21:08:09 UTC  
> Updated at: 2015-10-08 11:25:04 UTC  
> Closed at: 2015-10-08 11:25:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/17  

`some_function() noexcept = default;` seems like a bad practice. Clang3.6 will fail compilation if  exception specification of defaulted function does not match the calculated one. GCC4.8 and before also fail compilation. Latest versions of GCC silently ignore the explicit exception specification.  
  
This pull request drops the `BOOST_NOEXCEPT` if followed by `= default`. This fixes the compilation errors like:  
  
```  
../../../boost/filesystem/operations.hpp:266:5: error: function ‘boost::filesystem::file_status::file_status(const boost::filesystem::file_status&)’ defaulted on its first declaration must not have an exception-specification  
  
../../../boost/filesystem/operations.hpp:267:18: error: function ‘boost::filesystem::file_status& boost::filesystem::file_status::operator=(const boost::filesystem::file_status&)’ defaulted on its first declaration must not have an exception-specification  
```

---

## Comment 1

> Username: Beman  
> Created_at: 2015-10-08 11:25:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/17#issuecomment-146508883  

Antony,  
  
Thanks for the pull request. I fixed the problem in a slightly different way; I'd already wasted too much time with "= default" problems so eliminated its use for now.  
  
Here is the commit message:  
  
Fix pull request 17, 'some_function() noexcept = default;' from Antony Polukhin, by never relying on "= default". Antony's pull request would have fixed the problem interaction with noexcept by eliminating the noexcept. I preferred to retain the noexcept rather than the "= default", as applying "= default" has been exceedingly troublesome for both VC++ and GCC due to interactions between the two C++11 features. GCC interactions varied not just for the version, but also for the platform.   
  
Thanks,  
  
--Beman

---
