# #68 Fixes of potential UB due to overflowing bit-shift operation [Merged]

> Username: mloskot  
> Created at: 2018-03-28 23:05:54 UTC  
> Updated at: 2018-04-13 22:18:58 UTC  
> Merged at: 2018-03-29 15:02:57 UTC  
> Closed at: 2018-03-29 15:02:57 UTC  
> Url: https://github.com/boostorg/gil/pull/68  

### Description  
  
 More fixes of potential UB due to overflowing bit-shift operation  
  
Patch courtesy of @Lastique via Boost ML.  
Continuation of SHA-1: 90c2deb725db4dda408b065e130f19553574953d  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
### References (eg. other issues, pull requests)  
  
#46, #50, #51 and hopefully it also fixes the checksum bug #49

---

## Comment 1

> Username: chhenning  
> Created_at: 2018-03-28 23:14:24 UTC  
> Url: https://github.com/boostorg/gil/pull/68#issuecomment-377068535  

It's really hard to see any actual fix in between all the line trimmings. Can that be separated?

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-03-28 23:16:06 UTC  
> Url: https://github.com/boostorg/gil/pull/68#issuecomment-377068838  

Oh my, I did not notice. It must be the VSCode + .editorconfig auto-trimmed the trailing whitespaces. I'll fix it.

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-03-28 23:21:47 UTC  
> Url: https://github.com/boostorg/gil/pull/68#issuecomment-377069993  

@chhenning should be fixed now. Thanks for spotting.

---

## Review 4 [Commented]

> Username: Lastique  
> Created_at: 2018-03-29 01:59:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/68#pullrequestreview-107905560  

📁 include/boost/gil/channel_algorithm.hpp

```diff
 354 |     typedef bits32s type;
 355 |-     type operator()(bits32 x) const { return static_cast<bits32s>(x-(1<<31)); }
 355 |+     type operator()(bits32 x) const { return static_cast<bits32s>(x)+(static_cast<bits32>(1)<<31); }
```

> Username: Lastique  
> Created_at: 2018-03-29 01:59:56 UTC  
> Updated_at: 2018-03-29 14:54:13 UTC  
> Url: https://github.com/boostorg/gil/pull/68#discussion_r177936721  

This changed `-` to `+`. With unsigned 32-bit overflow semantics, the result would be the same, but the change might not have been intentional nonetheless.  
  
Also, the way this change is applied in this particular case actually does not improve the code because you cast the previously unsigned `x` to signed and then add an unsigned value that is not representable as `bits32s`, i.e. cause `x` to be converted back to unsigned. As suggested on the ML, you should do the math on unsigned types. In this case you will have to cast to signed as the last step. This cast is technically an implementation-defined behavior if the unsigned value does not fit in the non-negative range of the target signed type, but in most cases you can hope for bitwise-equivalent result.

> Username: mloskot  
> Created_at: 2018-03-29 08:23:10 UTC  
> Updated_at: 2018-03-29 14:54:13 UTC  
> Url: https://github.com/boostorg/gil/pull/68#discussion_r177983931  

@Lastique I've made the mistake last night. It should be corrected. Thank you for catching it!


---

## Review 5 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-03-29 14:41:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/68#pullrequestreview-108075314  

📁 include/boost/gil/channel_algorithm.hpp

```diff
 332 |-     type operator()(bits32s x) const { return static_cast<bits32>(x+(1<<31)); }
 332 |+     type operator()(bits32s x) const { return static_cast<bits32>(x)+(static_cast<bits32>(1)<<31); }
 333 | };
```

> Username: stefanseefeld  
> Created_at: 2018-03-29 14:41:44 UTC  
> Updated_at: 2018-03-29 14:54:13 UTC  
> Url: https://github.com/boostorg/gil/pull/68#discussion_r178078069  

I would prefer `1u` over `static_cast<bits32>(1)`, just for conciseness, but that's really nit-picky now.  
Other than that, the PR looks good to me, so I believe we should merge it, even though I don't see a specific test failure in our CI runs that this fixes.

> Username: mloskot  
> Created_at: 2018-03-29 14:55:27 UTC  
> Url: https://github.com/boostorg/gil/pull/68#discussion_r178083016  

Done. Commits squashed. PR updated.  
  
> I don't see a specific test failure in our CI runs that this fixes.  
  
I thought that this actually is related to #51 and fixes GCC build https://travis-ci.org/boostorg/gil/jobs/355009262


---
