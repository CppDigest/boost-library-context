# #250 Drone [Merged]

> Username: klemens-morgenstern  
> Created at: 2022-05-19 06:50:45 UTC  
> Updated at: 2022-08-17 03:44:30 UTC  
> Merged at: 2022-05-19 09:00:16 UTC  
> Closed at: 2022-05-19 09:00:16 UTC  
> Url: https://github.com/boostorg/process/pull/250  



---

## Review 1 [Commented]

> Username: Flamefire  
> Created_at: 2022-05-23 14:10:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/250#pullrequestreview-981836885  

📁 CMakeLists.txt

```diff
   7 | 
   8 | project(boost_process VERSION "${BOOST_SUPERPROJECT_VERSION}" LANGUAGES CXX)
   9 |+ find_package(Boost REQUIRED iostreams program_options filesystem system thread)
```

> Username: Flamefire  
> Created_at: 2022-05-23 14:10:20 UTC  
> Url: https://github.com/boostorg/process/pull/250#discussion_r879503414  

This breaks the superproject build on which you basically rely by using `BOOST_SUPERPROJECT_VERSION`  
That in turn also breaks dependent libraries

> Username: pdimov  
> Created_at: 2022-06-02 16:24:34 UTC  
> Url: https://github.com/boostorg/process/pull/250#discussion_r888152999  

Confirmed. The Boost CMake build is broken now, as a result of this change. Please fix.


---

## Review 2 [Commented]

> Username: kou  
> Created_at: 2022-08-17 03:44:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/250#pullrequestreview-1074982987  

📁 include/boost/process/environment.hpp

```diff
 265 |         {
 266 |-             if (std::equal(st1.begin(), st1.end(), *p))
 266 |+             const int len = std::char_traits<Char>::length(*p);
```

> Username: kou  
> Created_at: 2022-08-17 03:04:11 UTC  
> Updated_at: 2022-08-17 03:44:30 UTC  
> Url: https://github.com/boostorg/process/pull/250#discussion_r947407602  

Can we use `iterator::difference_type` here something like `const auto len = static_cast<iterator::difference_type>(std::char_traits<Char>::length(*p))`?  
This causes an warning with Visual C++:  
  
https://ci.appveyor.com/project/ApacheSoftwareFoundation/arrow/builds/44489657/job/xh0s898nasxat5fn#L1515  
  
```text  
C:\Miniconda37-x64\envs\arrow\Library\include\boost/process/environment.hpp(266): warning C4267: 'initializing': conversion from 'size_t' to 'int', possible loss of data  
C:\Miniconda37-x64\envs\arrow\Library\include\boost/process/environment.hpp(266): warning C4267: 'initializing': conversion from 'size_t' to 'const int', possible loss of data  
```

---

📁 include/boost/process/environment.hpp

```diff
 294 |         {
 291 |-             if (std::equal(st1.begin(), st1.end(), *p))
 295 |+             const int len = std::char_traits<Char>::length(*p);
```

> Username: kou  
> Created_at: 2022-08-17 03:04:26 UTC  
> Updated_at: 2022-08-17 03:44:31 UTC  
> Url: https://github.com/boostorg/process/pull/250#discussion_r947407676  

Ditto.

---

📁 include/boost/process/environment.hpp

```diff
 279 |         {
 278 |-             if (std::equal(st1.begin(), st1.end(), *p))
 280 |+             const int len = std::char_traits<Char>::length(*p);
```

> Username: kou  
> Created_at: 2022-08-17 03:04:28 UTC  
> Updated_at: 2022-08-17 03:44:31 UTC  
> Url: https://github.com/boostorg/process/pull/250#discussion_r947407688  

Ditto.


---
