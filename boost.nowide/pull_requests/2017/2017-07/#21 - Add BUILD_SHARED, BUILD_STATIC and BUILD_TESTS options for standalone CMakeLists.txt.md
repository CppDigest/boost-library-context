# #21 Add BUILD_SHARED, BUILD_STATIC and BUILD_TESTS options for standalone CMakeLists.txt [Closed]

> Username: ivaigult  
> Created at: 2017-07-18 11:28:12 UTC  
> Updated at: 2019-12-14 18:13:12 UTC  
> Closed at: 2019-12-14 18:13:12 UTC  
> Url: https://github.com/boostorg/nowide/pull/21  

This PR fixes #20 issue. This makes _nowide_ usable via `add_subdirectory` for me, since I can disable tests and shared library.  
  
Use `-DBUILD_SHARED=OFF` for disabling build of nowilde.dll.  
Use `-DBUILD_STATIC=OFF` for disabling build of nowilde-static.lib.  
Use `-DBUILD_TESTS=OFF` for disabling all the tests.  
  
You may also set those variables before adding _nowide_ as sub directory for your project.

---

## Comment 1

> Username: ivaigult  
> Created_at: 2017-08-11 11:02:38 UTC  
> Url: https://github.com/boostorg/nowide/pull/21#issuecomment-321784433  

@artyom-beilis, is this repo alive? Will it be merged in?

---

## Comment 2

> Username: Flamefire  
> Created_at: 2017-09-29 22:04:45 UTC  
> Url: https://github.com/boostorg/nowide/pull/21#issuecomment-333249872  

Just came by this. Some proper CMakeFiles would be great to just build this and its tests. Currently writing my own based on the standalone one but had to make adjustments too to make it work like disabling the auto_link or adding the boost::filestream test  
I'd generalize my CMakeLists if this project is still active enough and there is use for it

---

## Comment 3

> Username: Flamefire  
> Created_at: 2019-12-14 18:13:12 UTC  
> Url: https://github.com/boostorg/nowide/pull/21#issuecomment-565739573  

Superseded by #27. If there is any chance for improvements feel free to comment or open an issue.  
  
Note though: The "standalone" version requires a conversion step, so that cannot be used as a submodule. However I'll add releases here or in my fork that will have a tgz with the ready-to-use standalone version (see my fork)

---
