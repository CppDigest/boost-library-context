# #636 Add support for <filesystem> to IO [Merged]

> Username: mloskot  
> Created at: 2022-02-05 16:01:43 UTC  
> Updated at: 2022-02-22 18:18:59 UTC  
> Merged at: 2022-02-22 18:18:55 UTC  
> Closed at: 2022-02-22 18:18:55 UTC  
> Url: https://github.com/boostorg/gil/pull/636  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
If neither <filesystem> nor <experimental/filesystem> is detected,  
then require <boost/filesystem.hpp>.  
  
If user defines BOOST_GIL_IO_USE_BOOST_FILESYSTEM macro,  
then <boost/filesystem.hpp> is pre-selected and required,  
and search for any of the C++ standard implementation is skipped.  
  
Remove end-user macro BOOST_GIL_IO_ADD_FS_PATH_SUPPORT  
Require tests to always build with support of either  
detected C++ filesystem or pre-selected Boost.Filesystem.  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
- #222 - this PR removes `BOOST_GIL_IO_ADD_FS_PATH_SUPPORT` macro   
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add note to [RELEASES.md](https://github.com/boostorg/gil/blob/develop/RELEASES.md) changelog.  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Commented]

> Username: simmplecoder  
> Created_at: 2022-02-06 11:54:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/636#pullrequestreview-874028385  

📁 include/boost/gil/io/detail/filesystem.hpp

```diff
  12 |+ 
  13 |+ #if !defined(BOOST_GIL_IO_USE_BOOST_FILESYSTEM)
  14 |+ #if !defined(BOOST_NO_CXX17_HDR_FILESYSTEM) || defined(__cpp_lib_filesystem)
```

> Username: simmplecoder  
> Created_at: 2022-02-06 11:54:18 UTC  
> Updated_at: 2022-02-06 12:01:33 UTC  
> Url: https://github.com/boostorg/gil/pull/636#discussion_r800168417  

If I understood correctly, it seems like `BOOST_NO_CXX17_HDR_FILESYSTEM` is the boost wide flag and the one above is GIL specific flag, right? Perhaps we should somehow enforce consistency between the two? Also, it seems like the flags `BOOST_GIL_IO_USE_STD_FILESYSTEM`, `BOOST_GIL_IO_USE_STD_EXPERIMENTAL_FILESYSTEM` are not orthogonal. Perhaps we could have one flag that has 3 distinct values (BOOST_FILESYSTEM, STD_FILESYSTEM, STD_EXPERIMENTAL_FILESYSTEM) and if on those?

> Username: mloskot  
> Created_at: 2022-02-09 18:36:12 UTC  
> Updated_at: 2022-02-09 18:36:13 UTC  
> Url: https://github.com/boostorg/gil/pull/636#discussion_r802975885  

> it seems like BOOST_NO_CXX17_HDR_FILESYSTEM is the boost wide flag and the one above is GIL specific flag, right?  
  
Yes  
  
> Perhaps we should somehow enforce consistency between the two?  
  
Not sure what you mean.  
  
>  it seems like the flags BOOST_GIL_IO_USE_STD_FILESYSTEM, BOOST_GIL_IO_USE_STD_EXPERIMENTAL_FILESYSTEM are not orthogonal.  
  
Rather, the latter is a part (subset) of the former.  
  
> Perhaps we could have one flag that has 3 distinct values  
  
If it wasn't a preprocessor but e.g. Python, I'd love that idea :)  
With preprocessor, I tend to keep things dead simple, distinct names, which can be added or removed in future - IMO, it's easier to validate if a preprocessor macro exists, than if a set of values of a macro changed.


---

## Comment 2

> Username: simmplecoder  
> Created_at: 2022-02-06 12:03:51 UTC  
> Url: https://github.com/boostorg/gil/pull/636#issuecomment-1030818855  

The filesystem library requires separate flag for gcc 7, IIRC. It seems like the build flag will be on top of standard defines, right? So basically people can pass correct cmake flag into GIL's CML or use it from headers directly and deal with flags themselves. I believe doing it cmake only will make it much easier, but I guess since it is Boost it has do be done the most portable way.

---

## Comment 3

> Username: mloskot  
> Created_at: 2022-02-09 18:38:00 UTC  
> Updated_at: 2022-02-09 18:38:13 UTC  
> Url: https://github.com/boostorg/gil/pull/636#issuecomment-1034075685  

@simmplecoder   
> It seems like the build flag will be on top of standard defines, right?  
  
Yes, CMake option will accompany the C++ macro.  
Ideally, if there was no such option and not even macro, but  
- if there is any of the standard filesystem, use it  
- otherwise, assume Boost.Filesystem is used  
- if none available, fail hard  
  
> since it is Boost it has do be done the most portable way.  
  
Yes

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-02-22 08:47:15 UTC  
> Url: https://github.com/boostorg/gil/pull/636#issuecomment-1047556191  

@lpranam @simmplecoder @sdebionne and anyone interested, I'd like to push this forward, so following my earlier comment https://github.com/boostorg/gil/pull/636#issuecomment-1034075685, what do you think about handling the filesystem libraries this way:  
  
1. We KISS and we offer **no option** for CMake or Boost.Build to enable/disable support for filesystem  
2. If there is any of the standard filesystem-s, use it.  
3. Otherwise, auto-assume Boost.Filesystem is used  
4. If none available, compilation will fail hard  
  
Comments?

---

## Comment 5

> Username: meshtag  
> Created_at: 2022-02-22 11:16:28 UTC  
> Url: https://github.com/boostorg/gil/pull/636#issuecomment-1047690367  

I liked this idea of dealing with file system libraries completely on our own without bothering/nudging the user(basically getting rid of `ADD_FS_PATH_SUPPORT` macro). Proposed `BOOST_GIL_IO_USE_BOOST_FILESYSTEM` macro seems good for providing control if the user absolutely needs it.  
   
Regardless of the final decision in this matter, adding support for `std::filesystem` seems like a good idea to me.

---

## Comment 6

> Username: simmplecoder  
> Created_at: 2022-02-22 15:01:46 UTC  
> Url: https://github.com/boostorg/gil/pull/636#issuecomment-1047885924  

Great idea on pushing this forward in a simple form. As long as we do not close doors to important modifications (build system integration), CI seems to take priority.

---

## Comment 7

> Username: mloskot  
> Created_at: 2022-02-22 18:14:16 UTC  
> Url: https://github.com/boostorg/gil/pull/636#issuecomment-1048077652  

Thanks for the comments. I'm going to hit Merge and let's see what happens. This is part of the library that can be improved at later time, if necessary.

---
