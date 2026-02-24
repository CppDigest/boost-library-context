# #52 - Crash on Android when invoking the copy assignment operator of 'shared_library' [Open]

> Username: RazvanAM  
> Created at: 2021-08-02 07:45:47 UTC  
> Updated at: 2025-01-28 10:07:02 UTC  
> Url: https://github.com/boostorg/dll/issues/52  

When invoking the copy assignment operator (or the copy constructor) of the 'shared_library' class, eventually there is a call to 'boost::dll::detail::path_from_handle'. On android this call ends up dereferencing the handle returned by 'dlopen', which is not allowed since this patch: https://android.googlesource.com/platform/bionic/+/ae74e87%5E!/.  
  
As a side-note, I believe the 'BOOST_OS_ANDROID' macro is not longer used.
