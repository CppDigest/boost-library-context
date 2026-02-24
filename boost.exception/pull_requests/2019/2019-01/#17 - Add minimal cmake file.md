# #17 [CMake] Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2019-01-04 18:09:13 UTC  
> Updated at: 2019-03-03 06:32:01 UTC  
> Merged at: 2019-03-03 06:13:03 UTC  
> Closed at: 2019-03-03 06:13:03 UTC  
> Url: https://github.com/boostorg/exception/pull/17  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
    add_subdirectory( <path-to-boost_exception> )  
    target_link_libraries( <my_lib> PUBLIC Boost::exception )  
  
More information:  
  
- https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M%5B1-25%5D  
- https://groups.google.com/d/msg/boost-developers-archive/4HU-RzReL7U/FS1X6OFrEQAJ  
- https://groups.google.com/forum/#!topic/boost-developers-archive/9ZdrVbAn1aU  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2019-02-05 19:54:51 UTC  
> Url: https://github.com/boostorg/exception/pull/17#issuecomment-460780263  

Any comments?  
  
Do I understand the jam file correctly, that this library can only be used as a static library?

---

## Comment 2

> Username: zajo  
> Created_at: 2019-02-23 19:15:29 UTC  
> Url: https://github.com/boostorg/exception/pull/17#issuecomment-466681343  

I don't know cmake, will this always build the `clone_current_exception_non_intrusive.cpp` file? It should not, because that file only works on MSVC and even there it is optional, few people use it. It would be acceptable to leave it out of cmake.

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2019-02-24 11:22:08 UTC  
> Updated_at: 2019-02-24 11:23:11 UTC  
> Url: https://github.com/boostorg/exception/pull/17#issuecomment-466764936  

@zajo: Yes. The way it is currently written, it wil always build the file. As far as I understand, the b2 does the same no?   
  
I have no problem with removing it, but I don't quite see the advantage. The nice thing about cmake (and I guess most other higher level build systems) is that if my program `Foo` needs to use your library `Bar`, I just need to say `target_link_libraries(Foo PUBLIC  Bar)` - no matter if Bar is header only, a shared library or a static library - those things are just implementation details as far as the consumer is concerned (EDIT: Assuming both the library as well as the consumer are compiled with cmake).  
  
So, if compiling this file provides an advantage for some users and doesn't hurt the rest, I'd tend to prefer its  inclusion.   
  
 Of course it could also be made optional, but I generally try to avoid configuration options that don't provide big benefits, because they compound: Even if only every tenth boost library brings just a single configuartion option, there are more than 10.000 different configurations for boost as a whole (not counting things like compilation in different standards, shared/static library rtti etc) which makes it that much more likely to hit an untested/broken one or - if not everything is compiled together - make it more likely that my application tries to use two other libraries that expect conflicting configurations of boost.  
  
In any case: What matters in the end is what model you - as the maintainer of this library - prefer.   
The sensible options I see are:  
  
1) Ignore clone_current_exception_non_intrusive.cpp completely and treat exception as a header only library.  
2) Unconditionally compile `clone_current_exception_non_intrusive.cpp` (and I guess also define `BOOST_ENABLE_NON_INTRUSIVE_EXCEPTION_PTR` ).  
3) Use 1) on linux and 2) on windows.  
4) Allow the user to choose between 1) and 2)   
  
Just tell me which you prefer and I'll amned the PR accordingly.   
  
If you don't know cmake at all - should I add comments to the file? It is pretty standard stuff, but in the end you are the one that needs to maintain it.

---

## Comment 4

> Username: zajo  
> Created_at: 2019-02-28 05:15:25 UTC  
> Url: https://github.com/boostorg/exception/pull/17#issuecomment-468140725  

Ideally:  
  
If the user wants BOOST_ENABLE_NON_INTRUSIVE_EXCEPTION_PTR, and if the platform is Windows, and if the MSVC version used is one of the ones this thing works for (see https://github.com/boostorg/exception/blob/develop/src/clone_current_exception_non_intrusive.cpp#L32), then compile and link the cpp file, otherwise (and by default), not.  
  
Perfectly fine:  
  
Treat Boost Exception as header-only, do not include the cpp file, at least until someone complains.  
  
Thank you!

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2019-03-02 13:23:44 UTC  
> Url: https://github.com/boostorg/exception/pull/17#issuecomment-468920357  

Ok, I changed it to header only.   
  
I think keeping it simple is preferable for now.

---

## Comment 6

> Username: zajo  
> Created_at: 2019-03-03 06:13:06 UTC  
> Url: https://github.com/boostorg/exception/pull/17#issuecomment-468992793  

Thank you!

---

## Comment 7

> Username: Mike-Devel  
> Created_at: 2019-03-03 06:17:50 UTC  
> Url: https://github.com/boostorg/exception/pull/17#issuecomment-468992992  

Sorry, made a mistake:   
  
    target_include_directories( boost_exception PUBLIC include )  
  
  
should be   
  
    target_include_directories( boost_exception INTERFACE include )  
  
I was literally fixing it when you merged the PR ;)

---

## Comment 8

> Username: zajo  
> Created_at: 2019-03-03 06:26:45 UTC  
> Url: https://github.com/boostorg/exception/pull/17#issuecomment-468993368  

Can I bother you to submit another PR? thanks.

---

## Comment 9

> Username: Mike-Devel  
> Created_at: 2019-03-03 06:28:26 UTC  
> Url: https://github.com/boostorg/exception/pull/17#issuecomment-468993435  

Will try, but I'm currently on my mobile. Sorry for the mess.

---

## Comment 10

> Username: Mike-Devel  
> Created_at: 2019-03-03 06:32:01 UTC  
> Url: https://github.com/boostorg/exception/pull/17#issuecomment-468993624  

That was easier than expected: https://github.com/boostorg/exception/pull/20

---
