# #621 Fix for_each_pixel for non 1d iterable views [Merged]

> Username: sdebionne  
> Created at: 2021-07-16 15:19:45 UTC  
> Updated at: 2022-04-13 12:59:54 UTC  
> Merged at: 2022-03-01 13:35:21 UTC  
> Closed at: 2022-03-01 13:35:21 UTC  
> Url: https://github.com/boostorg/gil/pull/621  

### References  
  
Fix #620  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: sdebionne  
> Created_at: 2021-07-16 15:52:44 UTC  
> Url: https://github.com/boostorg/gil/pull/621#issuecomment-881547970  

@mloskot When the tests pass, I know we are pretty late in the dev cycle of 1.77 but any chance that this could be merged to master for 1.77?

---

## Comment 2

> Username: sdebionne  
> Created_at: 2021-07-17 05:59:33 UTC  
> Url: https://github.com/boostorg/gil/pull/621#issuecomment-881836944  

Some tests fails spurioulsy and generally unrelated to Boost.GIL, for instance clang/ubuntu reports:  
  
```  
/usr/bin/../lib/gcc/x86_64-linux-gnu/10/../../../../include/c++/10/memory:200:16: error: no member named 'is_constant_evaluated' in namespace 'std'  
      if (std::is_constant_evaluated())  
```  
  
or   
  
```  
error: toolset clang-linux initialization:  
error: provided command '"clang++-9"' not found  
```  
  
@lpranam There seems to be an issue with the numeric extension though, [MSVC complains](https://github.com/boostorg/gil/pull/621/checks?check_run_id=3087732919#step:4:647):  
  
```  
D:\a\gil\boost-root\boost/gil/extension/numeric/kernel.hpp(126): error C2338: kernel must have size greater than 0  
```

---

## Comment 3

> Username: sdebionne  
> Created_at: 2021-10-05 07:32:27 UTC  
> Url: https://github.com/boostorg/gil/pull/621#issuecomment-934144392  

@mloskot How about this one 😀  ?

---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2021-10-05 08:03:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/621#pullrequestreview-771106885  

Great, thanks!

---

## Comment 5

> Username: sdebionne  
> Created_at: 2022-03-01 13:29:08 UTC  
> Url: https://github.com/boostorg/gil/pull/621#issuecomment-1055444254  

@mloskot @simmplecoder Any chance that this could be merged? It fixes a pretty annoying bug (silently returns the wrong result)...

---

## Comment 6

> Username: mloskot  
> Created_at: 2022-03-01 13:30:43 UTC  
> Url: https://github.com/boostorg/gil/pull/621#issuecomment-1055445592  

Shoot, it's slipped through. Please, merge, of course.

---

## Comment 7

> Username: mloskot  
> Created_at: 2022-04-12 16:28:46 UTC  
> Url: https://github.com/boostorg/gil/pull/621#issuecomment-1096942965  

@sdebionne   
> Some tests fails spurioulsy and generally unrelated to Boost.GIL, for instance clang/ubuntu reports:  
> ```  
> /usr/bin/../lib/gcc/x86_64-linux-gnu/10/../../../../include/c++/10/memory:200:16: error: no member named 'is_constant_evaluated' in namespace 'std'  
>       if (std::is_constant_evaluated())  
> ```  
  
I noticed the same issue using clang 8 but with `cxxstd=2a`  
  
```  
clang-linux.compile.c++ bin.v2/libs/filesystem/build/clang-linux-8/release/cxxstd-2a-iso/threading-multi/visibility-hidden/operations.o  
In file included from libs/filesystem/src/operations.cpp:23:  
In file included from ./boost/smart_ptr/scoped_ptr.hpp:22:  
/usr/bin/../lib/gcc/x86_64-linux-gnu/10/../../../../include/c++/10/memory:200:16: error: no member named 'is_constant_evaluated' in namespace 'std'  
      if (std::is_constant_evaluated())  
          ~~~~~^  
1 error generated.  
  
  "clang++-8" -c -x c++ -std=c++2a -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW -DBOOST_FILESYSTEM_HAS_INIT_PRIORITY -DBOOST_FILESYSTEM_HAS_STATX -DBOOST_FILESYSTEM_HAS_STAT_ST_BLKSIZE -DBOOST_FILESYSTEM_HAS_STAT_ST_MTIM -DBOOST_FILESYSTEM_SOURCE -DNDEBUG -I"." -I"libs/filesystem/src"   -o "bin.v2/libs/filesystem/build/clang-linux-8/release/cxxstd-2a-iso/threading-multi/visibility-hidden/operations.o" "libs/filesystem/src/operations.cpp"  
  
...failed clang-linux.compile.c++ bin.v2/libs/filesystem/build/clang-linux-8/release/cxxstd-2a-iso/threading-multi/visibility-hidden/operations.o...  
```  
  
[clang-8-cxxstd-2a.zip](https://github.com/boostorg/gil/files/8474809/clang-8-cxxstd-2a.zip)

---

## Comment 8

> Username: sdebionne  
> Created_at: 2022-04-13 06:26:45 UTC  
> Url: https://github.com/boostorg/gil/pull/621#issuecomment-1097606872  

Are these issues related to partially implemented std / standard library? Again I am super happy with all the work being done on the CI, this will make the project more attractive to users and developers.

---

## Comment 9

> Username: mloskot  
> Created_at: 2022-04-13 12:59:54 UTC  
> Url: https://github.com/boostorg/gil/pull/621#issuecomment-1098020888  

> Are these issues related to partially implemented std / standard library?   
  
I'm not 100% sure, but looks like it.

---
