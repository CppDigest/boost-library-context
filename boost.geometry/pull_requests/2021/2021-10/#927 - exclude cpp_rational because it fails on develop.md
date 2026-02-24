# #927 [test] exclude cpp_rational because it fails on develop [Merged]

> Username: barendgehrels  
> Created at: 2021-10-20 12:10:00 UTC  
> Updated at: 2021-10-27 21:57:16 UTC  
> Merged at: 2021-10-26 15:30:31 UTC  
> Closed at: 2021-10-26 15:30:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/927  

See discussions in #917, I propose to disable this test for now.  
We still test the (probably more used) Boost MP types (bm::int128_t, bm::cpp_bin_float, bm::cpp_dec_float)

---

## Comment 1

> Username: awulkiew  
> Created_at: 2021-10-20 13:52:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/927#issuecomment-947689110  

We still should probably see why does it fail or at least create an issue at MultiPrecision.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2021-10-26 15:30:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/927#issuecomment-952057336  

Merging, assuming it's OK

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2021-10-26 15:31:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/927#issuecomment-952057711  

> We still should probably see why does it fail or at least create an issue at MultiPrecision.  
  
A ticket would be good. Could you create a ticket?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2021-10-26 19:12:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/927#issuecomment-952233630  

Done: https://github.com/boostorg/multiprecision/issues/387

---

## Comment 5

> Username: awulkiew  
> Created_at: 2021-10-27 21:51:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/927#issuecomment-953338291  

@barendgehrels it is fixed in MP. It was caused by the regression in the default constructor of `rational_adaptor` which initialized the number to 1 instead of 0.  
  
And since we're on the subject. Granted this was a regression but I think we should stop initializing numeric types using default ctor and instead we should be assigning 0 explicitly. Right now we do different things in different places. In general it is not guaranteed that a default-constructed user-defined numeric type will be equal to 0.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2021-10-27 21:57:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/927#issuecomment-953340977  

I enabled the test already: https://github.com/boostorg/geometry/commit/b5d27931909265818fd06f0c4eab495ac025d990

---
