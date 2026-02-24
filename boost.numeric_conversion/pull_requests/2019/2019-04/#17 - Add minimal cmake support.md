# #17 [CMake] Add minimal cmake support [Closed]

> Username: Mike-Devel  
> Created at: 2019-04-16 10:59:51 UTC  
> Updated at: 2023-02-04 15:46:06 UTC  
> Closed at: 2022-11-16 00:14:17 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/17  

- CMake file only supports `add_subdirectory` workflow.  
- Provides target `Boost::numeric_conversion` to "link" against (effectively providing the include directory of numeric_conversion and information about its dependencies).  
- Doesn't support installation (That is taken care of via b2).  
  
Note: As the unit test depend on Boost.Test, which in turn depends on numeric_conversion, I think it doesn't make sense - for now - to run the unit tests from cmake even though it would be possible in principle by relying on a preinstalled version of Boost.Test.

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2019-05-05 09:53:56 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/17#issuecomment-489410301  

Ping

---

## Comment 2

> Username: brandon-kohn  
> Created_at: 2019-06-12 14:29:58 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/17#issuecomment-501299866  

I'm certainly open to this, though I don't know what consensus has been made about how these files should be structured. I recall there was some debate about this and using the build subdirectory in each lib. I don't remember the details. @fcacciola what do you think? I think it certainly couldn't hurt to add this; with the caveat that it is subject to change completely depending on how cmake support officially evolves in the boost dev community.

---

## Comment 3

> Username: brandon-kohn  
> Created_at: 2019-06-12 14:31:36 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/17#issuecomment-501300554  

As for the circular dependency, I believe the compiled form of Boost.Test has been deprecated. So header only going forward. Does that resolve your issue with testing?

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2019-06-13 10:30:35 UTC  
> Updated_at: 2019-06-13 10:31:28 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/17#issuecomment-501646691  

> I recall there was some debate about this and using the build subdirectory in each lib.   
  
There have been several debates over the years, and I doubt I've read them all, so I'm not sure what you are referring to.   
  
But to be clear: My current PRs are **not** endorsed as such by the Boost mailing list, and they are certainly not going to provide a full cmake solution for boost (efforts by other people in that direction have stalled repeatedly). This is more about establishing a minimal cmake skeleton, such that boost libraries that want to support a cmake-only dev/build flow have an easy way to refer to their boost-internal dependencies.  
  
Here: https://github.com/Mike-Devel/boost_dep_graph you can see a (not quite up to date) picture of the libraries that already have a cmake file in their root folder (Note: Some of them are much older than this min cmake effort and e.g. rely on some existing boost installation).  
  
Here: https://github.com/boostorg/boost/pull/193/files You can see what a user/developer would have to add to the boost root folder to make easy use of these local cmake files.  
  
> As for the circular dependency, I believe the compiled form of Boost.Test has been deprecated. So header only going forward. Does that resolve your issue with testing?  
  
Not really. On a cmake level, your tests would still have a dependency on the (not yet existing) target `Boost::test` the assumption here is that the cmake build runs without a prior invocation of `b2 headers` or some such, which means the headers still reside in the directory of boost.Test. Of course that doesn't mean you can't e.g. just guess their location, but unless you really want to support cmake based development of Boost.NumericConversion right now, I'd delay the unit test question for now.  
  
> with the caveat that it is subject to change completely depending on how cmake support officially evolves in the boost dev community.  
  
Absolutely. I've refrained from putting a cmake warning message into each individual cmake file, as it produces too much noise. But I did put a banner into the root cmake file I linked above and if you want, we can make my note in this file about this being totally experimental  more pronounced   
  
https://github.com/boostorg/numeric_conversion/blob/d67a5b510d5380277978197d7ec21f6c5e6150b1/CMakeLists.txt#L5-L6

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2019-06-24 17:31:43 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/17#issuecomment-505105962  

So? Any opinions on how to proceed?

---

## Comment 6

> Username: Mike-Devel  
> Created_at: 2019-12-08 13:11:42 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/17#issuecomment-562946874  

Ping

---

## Comment 7

> Username: brandon-kohn  
> Created_at: 2019-12-31 14:29:31 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/17#issuecomment-569937384  

I don't see this moving forward until there is consensus within the boost community on adding cmake support.

---

## Comment 8

> Username: Mike-Devel  
> Created_at: 2020-01-01 18:03:36 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/17#issuecomment-570071758  

Not sure, what counts as consensus, but I think about half of the boost libraries already support cmake in one fashion or another and the superproject has recently gained cmake support thanks to Peter Dimov too (https://github.com/boostorg/boost/blob/master/CMakeLists.txt, https://github.com/boostorg/cmake).   
  
So I'd say there is certainly support behind the idea of adding cmake support.

---

## Comment 9

> Username: umed  
> Created_at: 2020-03-06 14:49:09 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/17#issuecomment-595801074  

why is this still not merged?

---

## Comment 10

> Username: Mike-Devel  
> Created_at: 2020-11-04 07:22:06 UTC  
> Updated_at: 2020-11-04 07:24:16 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/17#issuecomment-721558799  

@brandon-kohn :   
  
> I don't see this moving forward until there is consensus within the boost community on adding cmake support.  
  
If you tell me what you would qualify as "within the boost community on adding cmake support", I can see if I can get it.

---
