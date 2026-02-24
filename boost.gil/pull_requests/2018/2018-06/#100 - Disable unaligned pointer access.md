# #100 Disable unaligned pointer access [Merged]

> Username: mloskot  
> Created at: 2018-06-21 22:40:49 UTC  
> Updated at: 2019-05-07 14:14:25 UTC  
> Merged at: 2018-06-22 22:07:03 UTC  
> Closed at: 2018-06-22 22:07:03 UTC  
> Url: https://github.com/boostorg/gil/pull/100  

(NOTE: _Assigned to self - if accepted, I'd like to merge myself. Thanks._)  
  
### Description: what does this pull request do?  
  
Rename `GIL_NONWORD_POINTER_ALIGNMENT_SUPPORTED`   to `BOOST_GIL_CONFIG_HAS_UNALIGNED_ACCESS`.  
Undefine `BOOST_GIL_CONFIG_HAS_UNALIGNED_ACCESS` by default and document it.  
If defined, issue warning or error, depending on target platform.  
  
This changes how `packed_channel_reference_base`-based channels perform access - aligned memory access by default.  
  
This also fixes undefined behavior detected by UBSan in some test cases.  
  
-----  
  
Accessing a misaligned pointer results in undefined behavior and causes numerous [UBSan sanitizer run-time errors](https://travis-ci.org/boostorg/gil/jobs/395130083) on Travis CI (and locally too).  
  
For example:  
  
```  
channel.hpp:348:70: runtime error: reference binding to misaligned address 0x0000029c2cf1 for type  
  'const boost::gil::detail::packed_channel_reference_base<boost::gil::packed_dynamic_channel_reference<unsigned short, 1, true>, unsigned short, 1, true>::bitfield_t' (aka 'const unsigned short'),  
  which requires 2 byte alignment 0x0000029c2cf1  
```  
  
### Tasklist  
  
- [x] Disable `GIL_NONWORD_POINTER_ALIGNMENT_SUPPORTED`  by default.  
- [x] Rename `GIL_NONWORD_POINTER_ALIGNMENT_SUPPORTED` to clearer `BOOST_GIL_CONFIG_HAS_UNALIGNED_ACCESS`  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed (minus `ubsan_integer` still expected to fail)  
  
### Questions  
  
Apparently, the 10+ years old rationale to enable misaligned access is improved performance:  
  
https://github.com/boostorg/gil/blob/d882b844944001f5f94e55aed3faaf651aae3032/include/boost/gil/gil_config.hpp#L28-L32  
  
But, is it?  
  
The unaligned access is not cheap either, but it is troublesome due to the _undefined behaviour_ in C and C++. Unfortunately, I don't have time to prepare GIL-specific benchmark to compare performance with and without `GIL_NONWORD_POINTER_ALIGNMENT_SUPPORTED` defined.  
  
However, shall we review it and perhaps agree to disable `GIL_NONWORD_POINTER_ALIGNMENT_SUPPORTED` by default?  
  
-----  
  
BTW, I have added `-Wcast-align` to `<cxxflags>` in the Jamfile, see https://github.com/boostorg/gil/commit/e385653a239405af207ab0bc86a1fe5e80ae217d, but so far I haven't noticed any related warnings reported.

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-06-21 22:47:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/100#pullrequestreview-131019378  

I fully agree: I don't understand the exact semantics of this, and as we need to avoid undefined behaviour, I think disabling this flag is a good choice in the short term.  
Long-term we can certainly revisit this by enabling the flag and perform appropriate tests better understand the impact.

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-06-22 04:47:01 UTC  
> Url: https://github.com/boostorg/gil/pull/100#issuecomment-399319371  

To summary the latest Travis CI builds, with `GIL_NONWORD_POINTER_ALIGNMENT_SUPPORTED`:  
- enabled the `variant=ubsan_undefined` reports sanitizer runtime failures: https://travis-ci.org/boostorg/gil/jobs/395253344  
- disabled the `variant=ubsan_undefined` reports no errors: https://travis-ci.org/boostorg/gil/builds/395253646  
  
I assume no objections to **disable** ``GIL_NONWORD_POINTER_ALIGNMENT_SUPPORTED` then.  
  
I am completing complete this PR and will merge it soon

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-06-22 22:03:37 UTC  
> Url: https://github.com/boostorg/gil/pull/100#issuecomment-399594626  

It's been tweaked and is ready.  
@stefanseefeld still OK to merge?

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2018-06-22 22:05:50 UTC  
> Url: https://github.com/boostorg/gil/pull/100#issuecomment-399595025  

Sure !

---
