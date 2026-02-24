# #10 [CMake] Generate cmake target that other libraries can use [Merged]

> Username: Mike-Devel  
> Created at: 2018-09-17 09:41:19 UTC  
> Updated at: 2018-09-20 09:57:11 UTC  
> Merged at: 2018-09-19 21:55:35 UTC  
> Closed at: 2018-09-19 21:55:36 UTC  
> Url: https://github.com/boostorg/assert/pull/10  

... to express their dependency on this library and retrieve any  
configuration information such as the include directory, binary  
to link to (if any), transitive dependencies, necessary compiler  
options or the required c++ standards level.  
  
More info: https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2018-09-17 10:15:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/assert/pull/10#pullrequestreview-155858257  

📁 CMakeLists.txt

```diff
   1 |+ cmake_minimum_required(VERSION 3.8)
```

> Username: pdimov  
> Created_at: 2018-09-17 10:15:14 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218015645  

Why is the minimum 3.8?

> Username: Mike-Devel  
> Created_at: 2018-09-17 10:23:07 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218017508  

Consitency my other cmake files mostly.   
3.8 Introduced the compiler features `cxx_std_XX` to specify the required language standard as opposed to individual features.  
  
For this particular CMakeFile, 2.8.12 would probably be enough (alhtough I can't test it right now)

> Username: pdimov  
> Created_at: 2018-09-17 10:32:22 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218019963  

Requiring a higher version than needed is very annoying for people using the preinstalled cmake.  
  
Not sure about 2.8.12 though; I thought INTERFACE was newer.

> Username: Mike-Devel  
> Created_at: 2018-09-17 10:57:11 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218026037  

To what preinstalled version are you referring? 3.5 from ubuntu 16.04 LTS?   
  
You are probably right that 2.8.12 didn't have interface libraries

> Username: pdimov  
> Created_at: 2018-09-17 12:25:00 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218047633  

2.8.12 doesn't seem to have interface libraries: https://cmake.org/cmake/help/v2.8.12/cmake.html#command:add_library  
3.0 does however: https://cmake.org/cmake/help/v3.0/command/add_library.html

> Username: Mike-Devel  
> Created_at: 2018-09-18 13:33:46 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218433032  

Is 3.5 an acceptable compromise then?   
I don't want any future contributor to have the feeling he can't use modern cmake functionalities just because this initial version didn't require anything more recent. Conversely, I don't want a user to rely on compilation with outdated cmake versions. Particularly considering that it will probably take quite some time until this reaches "regular" developers anyway.

> Username: pdimov  
> Created_at: 2018-09-18 18:25:02 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218543686  

3.5 seems fine but I wonder what we need from 3.5 specifically. https://cmake.org/cmake/help/v3.12/manual/cmake-policies.7.html doesn't mention it at all.

> Username: pdimov  
> Created_at: 2018-09-19 15:14:05 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218845567  

OK let's go with 3.5 and be done with it. And `BoostAssert` as the project name - so that the config file is called `BoostAssertConfig.cmake` when we get to it as this seems to be the convention nowadays.

> Username: Mike-Devel  
> Created_at: 2018-09-19 16:35:31 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218876707  

Ok, I made the changes.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2018-09-17 10:22:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/assert/pull/10#pullrequestreview-155860315  

📁 CMakeLists.txt

```diff
   1 |+ cmake_minimum_required(VERSION 3.8)
   2 |+ project(boost-assert)
```

> Username: pdimov  
> Created_at: 2018-09-17 10:22:15 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218017262  

And... why does the project name use dash?

> Username: Mike-Devel  
> Created_at: 2018-09-17 10:24:09 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218017763  

What would you prefer instead?

> Username: pdimov  
> Created_at: 2018-09-17 10:33:37 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218020214  

No idea; I'm not up to date with CMake naming conventions. I was just wondering. Can we use `boost_assert` here as well, or does it have to be different?

> Username: Mike-Devel  
> Created_at: 2018-09-17 10:46:13 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218023138  

Unfortunately, I'm not aware of any common naming scheme for cmake libraries. I prefer to have a different name for the project than the targets, but as far as I know, you can use pretty much anything you want for a name (afaik this isn't visible to the consumer anyway).   
  
Other names I had considered are   
  
BoostAssert  
  
Or  
  
Boost.Assert  
  
I wasn't sure if there is any reason to avoid dots in the name, but as I haven't used them so far in project names I didn't feel like experimenting.

> Username: pdimov  
> Created_at: 2018-09-17 10:52:22 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218024759  

Looking at https://github.com/pdimov/boost-cmake-demo/blob/master/libs/assert/CMakeLists.txt, I've used `boost_assert` for the project name there as well. It does have the advantage of avoiding the "why is one _ and the other -" questions. :-)

> Username: Mike-Devel  
> Created_at: 2018-09-17 11:05:20 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218028128  

Well, they are different things (e.g. one project usually has multiple different targets anyway), so I prefer to name them differently. But I have really no problem with changing the project name to whatever you prefer


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2018-09-17 11:40:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/assert/pull/10#pullrequestreview-155883172  

📁 CMakeLists.txt

```diff
   5 |+ add_library(Boost::assert ALIAS boost_assert)
   6 |+ 
   7 |+ target_include_directories(boost_assert INTERFACE include)
```

> Username: pdimov  
> Created_at: 2018-09-17 11:40:00 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218036845  

Some people use `${CMAKE_CURRENT_SOURCE_DIR}/include` here, others `$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>`. Not sure which is correct, or what the difference is.

> Username: pdimov  
> Created_at: 2018-09-17 12:11:52 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218044475  

Word on the street is that the correct form is  
  
```  
target_include_directories(  
  ${LIBNAME}  
  PUBLIC  
  $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/include>  
  $<INSTALL_INTERFACE:include>  
  )  
```

> Username: pdimov  
> Created_at: 2018-09-17 13:25:06 UTC  
> Updated_at: 2018-09-19 16:34:55 UTC  
> Url: https://github.com/boostorg/assert/pull/10#discussion_r218066453  

Other word on the street is that the simple form as above does work and is equivalent to the example in the documentation, so we'll keep that and see.


---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2018-09-19 16:51:28 UTC  
> Url: https://github.com/boostorg/assert/pull/10#issuecomment-422876711  

Merging this PR probably doesn't make sense until the boost config PR is merged - otherwise the dependency is not available.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-09-19 21:56:43 UTC  
> Url: https://github.com/boostorg/assert/pull/10#issuecomment-422972271  

All files need a copyright/license boilerplate at the top. How do you want to be credited, under what name?

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-09-20 00:19:03 UTC  
> Url: https://github.com/boostorg/assert/pull/10#issuecomment-422998986  

Also, don't use tabs.

---

## Comment 7

> Username: Mike-Devel  
> Created_at: 2018-09-20 09:57:11 UTC  
> Url: https://github.com/boostorg/assert/pull/10#issuecomment-423120998  

Thanks for merging.   
Personally, I don't need my name mentioned for a trivial contribution like that (your extension is already bigger than the original file), but if you want (and that is ok with general guidelines) you can add my alias Mike Dev to the copy right notice.

---
