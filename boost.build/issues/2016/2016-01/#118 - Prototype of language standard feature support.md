# #118 - Prototype of language standard feature support. [Closed]

> Username: tee3  
> Created at: 2016-01-04 05:10:41 UTC  
> Updated at: 2019-02-06 14:11:20 UTC  
> Closed at: 2019-02-06 14:11:19 UTC  
> Url: https://github.com/boostorg/build/issues/118  

# Overview  
  
I spent a little time trying to implement a language standard feature and I was hoping someone might find some time to review the overall approach.  If it is a viable approach, there would be a bit more work to complete it.  It is current working for the `gcc` toolset as a proof of concept.  I think it looks like a reasonable approach.  
  
See https://github.com/tee3/build/tree/develop-language-standard,  
  
Currently, only C, C++, Objective-C, and Objective-C++ are supported.  Other languages could be implemented in a similar way.  
  
The language standard features are named as follows:  
- `<cstd>` - `c89`, `c90`, `gnu89`, `msvc89`, etc.  
- `<ccxxstd>` -  `c++98`, `c++03`, `gnu++98`, `msvc++98`, etc.  
- `<objcstd>` - `objc1`, `objc2`  
  - the C language standards also apply  
    - combinations of C standard and Objective-C standards is not tested yet  
- `<objcxxstd>` - `objc1`, `objc2`  
  - the C++ language standards also apply  
    - combinations of C++ standard and Objective-C++ standards is not tested yet  
# Status:  
- only `gcc` is implemented   
  - `darwin`, `clang-linux`, `clang-darwin`, `intel-linux`, `intel-darwin` all inherit flags, so these work too  
  - does not yet use the compiler version to further restrict supported versions  
- only `clang` and `darwin` on OS X are tested so far  
- failures result in a compiler failure that hopefully is readable  
  
I created a test harness for this change externally and a Travis CI build.  
- https://github.com/tee3/boost-build-toolset-tester-std  
  - this generates files and compiles them to verify that the right version was selected  
    - more work has to be done here to ensure that extensions are not enabled when not requested  
- https://travis-ci.org/tee3/boost-build-toolset-tester-std/jobs/100043766  
  - the failing tests are expected in most cases (_eg_, `gcc` doesn't support `msvc` dialects)  
    - would be nice to fail expectedly on unsupported language standards  
# To Do  
- [ ] review and refine the language standard names for each lanuage  
- [ ] implement version-based refinement of language standards in `gcc`  
- [ ] implement version-based refinement of language standards in `msvc`  
- [ ] implement prototypes of all other toolsets for public compilers  
- [ ] refine approach to unsupported language standard requests?  
- [ ] integrate test with Boost.Build test harness  
- [ ] add `builtin.jam` support for other languages (`fortran`, others?)  
- [ ] implement in Python as well  
- [ ] documentation

---

## Comment 1

> Username: tee3  
> Created at: 2016-01-04 05:41:20 UTC  
> Url: https://github.com/boostorg/build/issues/118#issuecomment-168587185  

Added a documentation to do.

---

## Comment 2

> Username: tee3  
> Created at: 2016-01-04 05:43:48 UTC  
> Url: https://github.com/boostorg/build/issues/118#issuecomment-168587354  

Fixed some typos in the original description.

---

## Comment 3

> Username: tee3  
> Created at: 2016-01-23 20:20:53 UTC  
> Url: https://github.com/boostorg/build/issues/118#issuecomment-174218354  

@vprus @swatanabe @jhunold, is there any interest in something like this?  If so, please take a look at the implementation.  I'd love to finish it if it has a chance to be merged, but I didn't want to go do all the little pieces without checking.  Thanks!

---

## Comment 4

> Username: vprus  
> Created at: 2016-02-02 06:51:00 UTC  
> Url: https://github.com/boostorg/build/issues/118#issuecomment-178409699  

I am interested in this in principle, but it is at second position in my pipeline (first want to sort out one remaining test failure and look at your CI changes).

---

## Comment 5

> Username: tee3  
> Created at: 2016-02-02 13:14:14 UTC  
> Url: https://github.com/boostorg/build/issues/118#issuecomment-178570521  

@vprus Thats great news!  In that case, I will try to get going on some of the to-do items above as best I can, probably starting with adding some documentation.

---

## Comment 6

> Username: tee3  
> Created at: 2016-02-04 14:05:30 UTC  
> Url: https://github.com/boostorg/build/issues/118#issuecomment-179853742  

I've rebased the branch onto the latest `develop` branch.

---

## Comment 7

> Username: tee3  
> Created at: 2017-10-26 17:00:48 UTC  
> Updated at: 2017-10-26 17:01:13 UTC  
> Url: https://github.com/boostorg/build/issues/118#issuecomment-339732025  

It looks like something generally like this for C++ only was implemented in https://github.com/boostorg/build/commit/bca2049f1dc2c2348141b065111d58dc0f85d5da.  I guess we can close this now?

---

## Comment 8

> Username: tee3  
> Created at: 2019-02-06 14:11:19 UTC  
> Url: https://github.com/boostorg/build/issues/118#issuecomment-461036182  

I'm closing this as there has been no activity for a long time.  I do think adding language standard features for all language supported would be useful (more than I've done here and more than we have in the latest Boost.Build), but this issue doesn't really describe that.  If someone wants to keep this open, feel free to re-open it or make another issue that lays out a larger plan.
