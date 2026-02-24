# #93 - CMake install target [Closed]

> Username: e-fominov  
> Created at: 2016-09-26 07:54:19 UTC  
> Updated at: 2017-04-11 02:13:13 UTC  
> Closed at: 2017-04-11 02:13:13 UTC  
> Url: https://github.com/boostorg/beast/issues/93  

Beast now has no "installation" target in CMakeFiles.txt. If I run `cmake --build . --target install` - i get an error.   
Even if Beast is header-only, it will be good to make it available to other projects so they will be able to use `find_package(Beast)`  
I know how to make it. If you agree - I will make PR

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-26 11:44:05 UTC  
> Url: https://github.com/boostorg/beast/issues/93#issuecomment-249547890  

What happens when Beast becomes part of Boost?

---

## Comment 2

> Username: e-fominov  
> Created at: 2016-09-26 11:50:30 UTC  
> Url: https://github.com/boostorg/beast/issues/93#issuecomment-249548942  

When it happen, users will use `find_package(Boost)'. You can look at Boost's hana library - it has [CMakeFiles.txt](https://github.com/boostorg/hana/blob/master/CMakeLists.txt#L176)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-09-26 11:51:57 UTC  
> Url: https://github.com/boostorg/beast/issues/93#issuecomment-249549219  

I guess it can't hurt then

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-04-11 02:13:13 UTC  
> Url: https://github.com/boostorg/beast/issues/93#issuecomment-293129722  

This is not something that I can support, sorry.
