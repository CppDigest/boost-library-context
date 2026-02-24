# #254 - Add UWP windows-api support for system and server family selection [Open]

> Username: jeking3  
> Created at: 2017-10-19 01:22:27 UTC  
> Updated at: 2017-10-19 01:28:49 UTC  
> Url: https://github.com/boostorg/build/issues/254  

In recent pull requests to Boost.Predef (https://github.com/boostorg/predef/pull/57, https://github.com/boostorg/predef/pull/64) support for the latest Windows UWP was added.  In addition, Boost.WinAPI has recent pull requests to support Windows UWP (https://github.com/boostorg/winapi/pull/57, https://github.com/boostorg/winapi/pull/61).  
  
The documentation for windows-api specifies support for [some but not all](http://www.boost.org/build/doc/html/bbv2/reference/tools.html#bbv2.reference.tools.compiler.msvc) current UWP development.  The last pull requests I could find for this were from 2014.  It is time to update this support again.  
  
Currently it says that `desktop`, `phone`, and `store` are supported; my assumption is that `store` translates to adding `WINAPI_FAMILY=WINAPI_FAMILY_PC_APP` to the list of preprocessor definitions for the build.
