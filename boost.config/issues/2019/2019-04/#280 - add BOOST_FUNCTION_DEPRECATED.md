# #280 - add BOOST_FUNCTION_DEPRECATED [Closed]

> Username: HDembinski  
> Created at: 2019-04-28 11:13:15 UTC  
> Updated at: 2022-10-05 12:26:36 UTC  
> Closed at: 2022-10-05 12:26:36 UTC  
> Url: https://github.com/boostorg/config/issues/280  

It would be great to have a cross-platform way give a deprecation warning at compile-time for pre-c++14 compilers. In C++14, there is `[[deprecated]]`, but older compilers already supported this with compiler-specific extensions, see e.g.:  
  
https://stackoverflow.com/questions/295120/c-mark-as-deprecated/21265197#21265197  
  
If it is hard to do, maybe not worth investing the time, but it could be very useful.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-30 20:03:44 UTC  
> Url: https://github.com/boostorg/config/issues/280#issuecomment-1232109933  

See https://github.com/boostorg/config/pull/442.
