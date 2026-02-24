# #392 Split CMake identities: superproject and 'developer' [Open]

> Username: jeremy-murphy  
> Created at: 2024-10-31 01:05:27 UTC  
> Updated at: 2025-10-27 00:14:29 UTC  
> Url: https://github.com/boostorg/graph/pull/392  

I prefer to use CMake as a build system, but the existing CMake files in Boost don't really help.  
  
1. I don't want to treat every library as an active project when I'm only working on one.  
2. I need the header files to be part of the project so that an IDE treats them as the source code of the project.  
  
I'm always maintaining some split-identity CMake like this in order to do work, so I thought I may as well merge it upstream if   
1. It's compatible with super-project plans for CMake, and  
2. other developers would find it useful.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2024-10-31 01:09:48 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-2448797507  

This is still somewhat broken, I haven't got the tests to compile properly yet, I think it's partly missing the required Boost.UTF macros, but also some unexpected `static_assert` failures.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2024-10-31 01:12:40 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-2448801991  

I actually don't know who else works on Boost.Graph _and_ prefers CMake, so I'll just tag a few recent contributors to see if they are interested: @jan-grimo @danielyxyang @brunom @sehe @jcdong98 @andrea-cassioli-maersk @vslashg

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2024-10-31 03:46:39 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-2448958332  

Peter, I assigned you because I assume you know about the super-project CMake business.

---

## Comment 4

> Username: andrea-cassioli-maersk  
> Created_at: 2024-10-31 07:28:44 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-2449203481  

I do work with CMake usually/

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-10-31 17:18:19 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-2450416242  

This looks OK at a first glance, but you should change  
```  
if (BOOST_SUPERPROJECT_VERSION)  
```  
to  
```  
if(NOT CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)  
```  
which checks whether this is the root project (as opposed to checking whether it's part of the Boost superproject.)  
  
In principle, Boost libraries are also usable without a superproject, as direct subprojects, although in this specific case this probably isn't very practical because of the number of dependencies.

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-10-31 17:20:48 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-2450420946  

Also, if you're going to make changes to CMakeLists.txt, you should first add CI jobs that verify that current uses aren't broken. Or I can add them for you if you prefer.

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2024-11-05 03:30:37 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-2456156494  

> Also, if you're going to make changes to CMakeLists.txt, you should first add CI jobs that verify that current uses aren't broken. Or I can add them for you if you prefer.  
  
If you have some pre-canned test, then yes, please go ahead!

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2024-11-05 03:32:57 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-2456158652  

> This looks OK at a first glance, but you should change  
>   
> ```  
> if (BOOST_SUPERPROJECT_VERSION)  
> ```  
>   
> to  
>   
> ```  
> if(NOT CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)  
> ```  
>   
> which checks whether this is the root project (as opposed to checking whether it's part of the Boost superproject.)  
>   
> In principle, Boost libraries are also usable without a superproject, as direct subprojects, although in this specific case this probably isn't very practical because of the number of dependencies.  
  
OK, sure, will do. I see there are some new variables with `PROJECT` in their name specifically for this purpose, but they require a fairly recent version of CMake, so I'll just go with whatever is supported by our minimum requirement.

---

## Comment 9

> Username: pdimov  
> Created_at: 2024-11-07 10:11:53 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-2461830266  

CI jobs for testing CMake subdir/install use added. I arbitrarily picked the `adj_list_edge_list_set.cpp` test as the `main.cpp` file in both cases.

---

## Comment 10

> Username: jeremy-murphy  
> Created_at: 2024-12-19 09:25:18 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-2553176097  

> CI jobs for testing CMake subdir/install use added. I arbitrarily picked the `adj_list_edge_list_set.cpp` test as the `main.cpp` file in both cases.  
  
Actually, I'm a bit confused -- where did you add them to?

---

## Comment 11

> Username: pdimov  
> Created_at: 2024-12-19 09:34:43 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-2553196411  

Here: https://github.com/boostorg/graph/commit/f1e51a08578a648c27ef61c0581adb9e8440cac4

---

## Comment 12

> Username: jeremy-murphy  
> Created_at: 2025-08-18 03:11:40 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-3194961026  

@andrea-cassioli-maersk have you tried using this?

---

## Comment 13

> Username: andrea-cassioli-maersk  
> Created_at: 2025-08-18 06:51:51 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-3195353887  

not really, but I can give it a shot time permitting

---

## Comment 14

> Username: jeremy-murphy  
> Created_at: 2025-08-18 07:42:35 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-3195509681  

If you could, that would be awesome, because I would like to merge it but so far I'm the only person that has used it.

---

## Comment 15

> Username: andrea-cassioli-maersk  
> Created_at: 2025-08-19 10:16:55 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-3200135803  

The Cmake itself seems to work, I get some compile errors like  
```  
      |                                                   ^  
In file included from /Users/andrea.cassioli/workspace/graph/test/adj_matrix_cc.cpp:9:  
/opt/homebrew/include/boost/graph/graph_concepts.hpp:142:13: error: use of undeclared identifier 'degree'  
  142 |         n = degree(v, cg);  
      |             ^  
  
```  
  
but it might be because I using the boost distribution form `brew` on OSX, could it be?

---

## Comment 16

> Username: jeremy-murphy  
> Created_at: 2025-08-21 03:14:05 UTC  
> Url: https://github.com/boostorg/graph/pull/392#issuecomment-3208850781  

Thanks for having a look. Hmmmm, I can't think of a good reason why distribution via `brew` would introduce problems but anything is possible.

---
