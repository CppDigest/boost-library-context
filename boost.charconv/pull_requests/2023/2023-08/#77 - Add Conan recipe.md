# #77 Add Conan recipe [Merged]

> Username: grisumbras  
> Created at: 2023-08-20 11:48:35 UTC  
> Updated at: 2023-08-23 12:38:02 UTC  
> Merged at: 2023-08-23 12:38:02 UTC  
> Closed at: 2023-08-23 12:38:02 UTC  
> Url: https://github.com/boostorg/charconv/pull/77  

This PR adds a Conan recipe (aka conanfile) that lets a user to build Charconv Conan package locally.  
  
Specific notes on some of the nuances:  
* Conan packages require a version. I've set the version to 1.0.0 arbitrarily.  
* The minimal requried Boost version I've put in the recipe is 1.82.0 if 1.83 is required, I can increase it.  
* The build is done using CMake, as it is simpler to imitate the presense of Boost superproject that way. Users are expected to either have their own CMake installation or to manually add CMake is a build requirement for the build. This is a standard practice in Conan recipes.  
* CMakeLists.txt doesn't have install comands, so I had to manually copy files.  
* The resulting binary package depends on Boost. Boost package has a lot of options and values of those options affect identity of the depending binary packages. It might be possible to make those options not affect Charconv package's ID, but I currently don't know how. It's not really a major issue, though, because Charconv builds very quickly.  
* On some configurations Charconv depends on quadmath, which is documented. It should be possible to add it as a system dependency, but to be honest it seems non-trivial to guess on package manager level if the system provides quadmath. So I currently left it as is.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-08-22 12:35:48 UTC  
> Url: https://github.com/boostorg/charconv/pull/77#issuecomment-1688101846  

> This PR adds a Conan recipe (aka conanfile) that lets a user to build Charconv Conan package locally.  
>   
> Specific notes on some of the nuances:  
>   
> * Conan packages require a version. I've set the version to 1.0.0 arbitrarily.  
  
Once I merge to master all that is needed is a tag of `1.0.0` right? I need the same for vcpkg.  
  
> * The minimal requried Boost version I've put in the recipe is 1.82.0 if 1.83 is required, I can increase it.  
> * The build is done using CMake, as it is simpler to imitate the presense of Boost superproject that way. Users are expected to either have their own CMake installation or to manually add CMake is a build requirement for the build. This is a standard practice in Conan recipes.  
> * CMakeLists.txt doesn't have install comands, so I had to manually copy files.  
  
To avoid the manual copying step is there anything more than:  
  
```  
  include(GNUInstallDirs)  
  install(DIRECTORY "include/" DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")  
```  
  
That needs to be added to the main CMakeLists.txt?   
  
> * The resulting binary package depends on Boost. Boost package has a lot of options and values of those options affect identity of the depending binary packages. It might be possible to make those options not affect Charconv package's ID, but I currently don't know how. It's not really a major issue, though, because Charconv builds very quickly.  
> * On some configurations Charconv depends on quadmath, which is documented. It should be possible to add it as a system dependency, but to be honest it seems non-trivial to guess on package manager level if the system provides quadmath. So I currently left it as is.  
  
I am going to assume that anyone working with `__float128` knows they need to have quadmath installed so no worries.

---

## Review 2 [Commented]

> Username: mborland  
> Created_at: 2023-08-22 12:38:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/77#pullrequestreview-1589340323  

📁 conan/conanfile.py

```diff
  48 |+             "clang": 6,
  49 |+             "Visual Studio": 14,  # guess
  50 |+             "msvc": 190,  # guess
```

> Username: mborland  
> Created_at: 2023-08-22 12:38:39 UTC  
> Updated_at: 2023-08-22 12:38:50 UTC  
> Url: https://github.com/boostorg/charconv/pull/77#discussion_r1301583108  

```diff  
-            "msvc": 190,  # guess  
+            "apple-clang": 99, # assume apple-clang will default to c++11 in the distant future  
+            "gcc": 5,  
+            "clang": 3.5,  
+            "Visual Studio": 14,  
+            "msvc": 190,  
```

> Username: grisumbras  
> Created_at: 2023-08-23 08:26:36 UTC  
> Url: https://github.com/boostorg/charconv/pull/77#discussion_r1302668460  

I've experimented with clang and gcc on CE, and they only default to >=C++11 since version 6.  
  
I can remove "guess" comments from MSVC lines if you wish.


---

## Comment 3

> Username: grisumbras  
> Created_at: 2023-08-23 08:15:04 UTC  
> Url: https://github.com/boostorg/charconv/pull/77#issuecomment-1689496415  

> Once I merge to master all that is needed is a tag of `1.0.0` right? I need the same for vcpkg.  
  
The tag is not strictly necessary. When Charconv is accepted into Boost this recipe can be removed, because Conan Center already has a Boost recipe. But sure, you can add a tag.  
  
> To avoid the manual copying step is there anything more than:  
>   
> ```  
>   include(GNUInstallDirs)  
>   install(DIRECTORY "include/" DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")  
> ```  
>   
> That needs to be added to the main CMakeLists.txt?  
  
Also   
```cmake  
install(TARGETS boost_charconv  
        RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"  
        LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"  
        ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}")  
```  
But I must warn you, that @pdimov doesn't like when Boost libraries' CMakeLists.txt contain install commands, because they can interfere with Superproject's own install system. It can also interfere with vcpkg portfile. I think we can leave it as-is, I just layed it out in case you wanted to better understand how my conanfile works.  
  
> I am going to assume that anyone working with `__float128` knows they need to have quadmath installed so no worries.  
  
I don't believe I was working with quadmath in my test program, I just used `from_chars` with a `double`. But in principle, I agree that it shouldn't be too hard to manually link to quadmath.

---

## Comment 4

> Username: mborland  
> Created_at: 2023-08-23 12:37:53 UTC  
> Url: https://github.com/boostorg/charconv/pull/77#issuecomment-1689887732  

Thanks for the feedback; I am just going to merge as is.

---
