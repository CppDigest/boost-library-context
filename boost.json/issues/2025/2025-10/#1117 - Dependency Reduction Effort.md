# #1117 - Dependency Reduction Effort [Closed]

> Username: vinniefalco  
> Created at: 2025-10-19 23:59:38 UTC  
> Updated at: 2025-10-27 11:51:31 UTC  
> Closed at: 2025-10-27 11:51:31 UTC  
> Url: https://github.com/boostorg/json/issues/1117  

Currently, Boost.JSON has these listed first-order dependencies according to boostdep:  
  
> align assert config container container_hash core describe endian mp11 static_assert system throw_exception  
  
We could explore the practicality of removing these from the list (by making appropriate changes):  
  
align endian static_assert

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-10-20 08:19:47 UTC  
> Url: https://github.com/boostorg/json/issues/1117#issuecomment-3421028289  

Align should be trivially removable, that's a good catch.  
  
StaticAssert can be simply eliminated too.  
  
Endian was mostly added because of the issue #1066.  But it makes sense to have Endian deal with endianness. That's the point of having such libraries, isn't it?  
  
Removing these dependencies doesn't give the user much, though. They are header-only, so very easy to install. All of their dependencies are still required either directly or through other dependencies.  
  
My suggestion: I remove Align and StaticAssert, but keep Endian.
