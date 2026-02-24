# #23 Made boost.iostreams Jamfile use proper bzip2 tool [Merged]

> Username: loonycyborg  
> Created at: 2015-12-04 23:18:47 UTC  
> Updated at: 2016-02-13 22:07:09 UTC  
> Merged at: 2016-02-13 22:07:09 UTC  
> Closed at: 2016-02-13 22:07:09 UTC  
> Url: https://github.com/boostorg/iostreams/pull/23  

This makes it so that there's a proper configuration check for bzip2 like there is for zlib. bzip tool is in another pull request: https://github.com/boostorg/build/pull/112  
New config check for zlib happened to work a lot better for me than old Jamfile code when building with mingw toolset. Unfortunately it was zlib only change and I need bzip support too, so I made those pull requests.

---
