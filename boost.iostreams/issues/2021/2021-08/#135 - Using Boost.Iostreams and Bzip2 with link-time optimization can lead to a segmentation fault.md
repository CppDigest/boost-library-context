# #135 - Using Boost.Iostreams and Bzip2 with link-time optimization can lead to a segmentation fault [Open]

> Username: Bihlerben  
> Created at: 2021-08-10 12:46:20 UTC  
> Updated at: 2022-02-01 19:03:43 UTC  
> Url: https://github.com/boostorg/iostreams/issues/135  

Configuration: boost 1_176_0 compiled with Mingw-w64 v7 and g++ 10.2 on Windows.  
  
The problem is discussed here: https://stackoverflow.com/questions/68723476/why-does-link-time-optimization-cause-a-segmentation-fault

---

## Comment 1

> Username: rdoeffinger  
> Created at: 2022-02-01 19:03:43 UTC  
> Url: https://github.com/boostorg/iostreams/issues/135#issuecomment-1027186548  

Does this happen when building without BZIP2_SOURCE, linking against a prebuilt bzip2?  
Bzip2 is fairly old code, it might very well not be compatible with such modern compiler features, or the boost build system might not do things that the bzip2 source needs (e.g. maybe -fno-strict-aliasing is required?).  
(I'm afraid I've always thought the BZIP2_SOURCE feature to be a bad idea, because it requires boost to all times be aware of the bzip2 source, its caveats, its build environment requirements etc etc and support it - it's not a feature that is reasonably maintainable, as nice as it may seem)
