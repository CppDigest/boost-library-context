# #196 - Boost::regex_icu for b2 builds [Closed]

> Username: anarthal  
> Created at: 2023-09-11 10:39:25 UTC  
> Updated at: 2023-11-12 17:32:44 UTC  
> Closed at: 2023-11-12 17:32:44 UTC  
> Url: https://github.com/boostorg/regex/issues/196  

I've been following [the docs](https://www.boost.org/doc/libs/1_83_0/libs/regex/doc/html/boost_regex/ref/non_std_strings/icu/intro.html) on how to link against Boost.Regex with ICU support but haven't been able to locate the `Boost::regex_icu` target. I'm using a Boost distribution generated using `b2`.  
  
From reading the code, it looks like the mentioned target is only present with Boost builds generated using cmake - could you please confirm? If that's the case, I suggest to update the docs, since they can be misleading otherwise.

---

## Comment 1

> Username: EternalSaga  
> Created at: 2023-11-06 09:13:51 UTC  
> Url: https://github.com/boostorg/regex/issues/196#issuecomment-1794377261  

I got the same error while using vcpkg.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-11-12 17:32:44 UTC  
> Url: https://github.com/boostorg/regex/issues/196#issuecomment-1807191105  

For clarification: Regex is a *header only library*, strictly speaking there is no need to link against anything.  However, CMake is slightly different in that all dependencies need to be listed, so... since the library may optionally depend on ICU we provide a convenience target in the CMake file which lists those dependencies.  There is no such target in the Jamfile, nor have we ever provided one, since that is typically only used to build the library - and in this case there is no longer anything to build.  
  
The documentation states: "CMake users should link to the Boost::regex_icu target in our CMakeLists.txt in order to have ICU dependencies taken care of when using this header."  which I think makes it pretty clear that this is for CMake only.
