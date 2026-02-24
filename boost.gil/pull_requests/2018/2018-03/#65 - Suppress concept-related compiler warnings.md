# #65 Suppress concept-related compiler warnings. [Merged]

> Username: stefanseefeld  
> Created at: 2018-03-28 21:15:40 UTC  
> Updated at: 2018-03-29 15:04:45 UTC  
> Merged at: 2018-03-29 15:04:45 UTC  
> Closed at: 2018-03-29 15:04:45 UTC  
> Url: https://github.com/boostorg/gil/pull/65  

This change suppresses lots of warnings on GCC all stemming from the use of (pseudo-)concepts to validate the use of template parameters in GIL code.

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2018-03-28 21:26:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/65#pullrequestreview-107856729  

Apart from the minor issue, LGTM.

📁 include/boost/gil/gil_concept.hpp

```diff
  34 | namespace boost { namespace gil {
  35 | 
  36 |+ #pragma GCC diagnostic push
```

> Username: mloskot  
> Created_at: 2018-03-28 21:25:46 UTC  
> Updated_at: 2018-03-29 12:15:22 UTC  
> Url: https://github.com/boostorg/gil/pull/65#discussion_r177895332  

This should be GCC-specific, otherwise "unknown pragma" will be issued eg. by MSVC  
  
```  
#if defined(__GNUC__) && (__GNUC__ >= 4)  
#pragma GCC diagnostic ignored "-Wunused-local-typedefs"  
#endif  
```


---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2018-03-29 12:24:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/65#pullrequestreview-108026157  

📁 include/boost/gil/extension/io/formats/pnm/write.hpp

```diff
 190 | 
 191 |-         typedef typename View::value_type pixel_t;
 191 |+         // typedef typename View::value_type pixel_t;
```

> Username: mloskot  
> Created_at: 2018-03-29 12:24:04 UTC  
> Updated_at: 2018-03-29 12:24:05 UTC  
> Url: https://github.com/boostorg/gil/pull/65#discussion_r178038512  

Why not just remove instead of commenting?  
  
Or, @chhenning perhaps used those typedefs as some sort of duck-typing check that necessary types are defined?

> Username: stefanseefeld  
> Created_at: 2018-03-29 12:26:30 UTC  
> Url: https://github.com/boostorg/gil/pull/65#discussion_r178039020  

Yeah, the commented-out code just below uses these typedefs, so I thought it unwise to remove it, just in case someone decides to un-comment that block.

> Username: mloskot  
> Created_at: 2018-03-29 13:01:37 UTC  
> Url: https://github.com/boostorg/gil/pull/65#discussion_r178047719  

Makes sense, thanks


---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2018-03-29 14:37:59 UTC  
> Url: https://github.com/boostorg/gil/pull/65#issuecomment-377256066  

@mloskot , so looking at the Travis logs, I see a considerable reduction in the amount of warnings. The AppVeyor builds still fail due to the checksum error, but this doesn't appear to be a regression. So it seems this is a step in the right direction. Can you please confirm ? (I have added a few minor commits to further reduce warnings. I haven't touched warnings from test code itself, though. Only library code.)

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-03-29 14:58:50 UTC  
> Url: https://github.com/boostorg/gil/pull/65#issuecomment-377263266  

@stefanseefeld Yes, I think you've summarized it correctly.

---
