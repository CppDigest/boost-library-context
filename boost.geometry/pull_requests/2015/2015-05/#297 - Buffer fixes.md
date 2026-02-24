# #297 Buffer fixes. [Merged]

> Username: awulkiew  
> Created at: 2015-05-19 23:06:40 UTC  
> Updated at: 2015-05-20 17:32:48 UTC  
> Merged at: 2015-05-20 15:19:23 UTC  
> Closed at: 2015-05-20 15:19:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/297  

The intention of this PR is to:  
- provide a temporary fix for msvc-8.0 caused by the use of Multiprecision. It replaces Multiprecision `cpp_int` with manually implemented solution.  
- suppress some compiler warnings.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-05-20 13:16:11 UTC  
> Updated_at: 2015-05-20 14:53:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30699542  

This is definitely not the best implementation of integral powers.  
I will try to come up with a better one.  
The problem is on the number of instantiations we have to make; it is linear in the power, whereas I believe it should be possible to do it with a logarithmic number.  
  
I am not sure it is that important though for the purposes of this fix.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-05-20 13:18:25 UTC  
> Updated_at: 2015-05-20 14:53:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30699781  

This looks like `2^half_bits` which is not the same as `(2^bits)/2`. I find the name a bit misleading.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-05-20 13:19:06 UTC  
> Updated_at: 2015-05-20 14:53:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30699842  

These do not follow our coding rules. They should be `m_sign`, `m_ms`, etc.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-05-20 13:19:50 UTC  
> Updated_at: 2015-05-20 14:53:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30699913  

See comment about coding rules fir local variables.  
I do not like the `_` at the end of the names. We rarely/never use them AFAIU.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-05-20 13:21:23 UTC  
> Updated_at: 2015-05-20 14:53:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30700045  

Is that part of the boost coding rules? I know that `std::min` and `std::max` need to go inside parentheses, but this is the same here.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-05-20 13:23:23 UTC  
> Updated_at: 2015-05-20 14:53:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30700225  

Any reference where the workaround is coming from?

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-05-20 13:24:51 UTC  
> Updated_at: 2015-05-20 14:53:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30700385  

Put a space after `!`.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-05-20 13:28:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#issuecomment-103888979  

Without knowing where the algorithm comes from, it looks good.  
In order to verify that the algorithm does indeed work, I would need this reference.  
  
The class `multiplicative_integral` should probably go in a different file in `boost/geometry/util`.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-05-20 13:51:10 UTC  
> Updated_at: 2015-05-20 14:53:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30703111  

If you're asking about `(std::numeric_limits<T>::min)` then the answer is yes, the reason is the same, to avoid macro substitution.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-05-20 13:58:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#issuecomment-103896708  

@mkaravel Thanks for your review, I agree with your points (also go into util)  
@awulkiew  Thanks for this, I'm OK with merging if comments are addressed

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-05-20 14:04:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#issuecomment-103898367  

The algorithm is adapted version of Karatsuba multiplication (http://en.wikipedia.org/wiki/Karatsuba_algorithm). I'll add a comment about it.  
  
The results can be verified after defining `BOOST_GEOMETRY_SIDE_OF_INTERSECTION_DEBUG`. Then the same values are calculated using Multiprecision and run-time assertions comparing the results are enabled.  
  
I don't think that it should go into util as it's not intended to be used in other parts of the library, at least in the current form. I fear that if I moved it into the util then we'd forget about it and it'd live there even after possible switching to Multiprecision. Or do you think it could be used somewhere else?

---

## Comment 12

> Username: awulkiew  
> Created_at: 2015-05-20 14:58:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30710987  

Yes, I think you're right that it should be possible to implement a version with lesser complexity. I also agree that this is not important as long as we don't plan to use it anywhere else in the library. If needed it could be moved e.g. to `util` and refactored.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-05-20 14:59:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30711209  

I renamed it simply to `base` since this is a base of numbers used in Karatsuba algorithm.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2015-05-20 15:19:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#issuecomment-103923519  

Ok, thanks for the review. I'll merge it to allow testing of the workaround and address any additional issues later.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-05-20 16:30:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30721116  

On second thought, you do not really need `integral_pow<>` at all.  
Simply do:  
  
```  
static const unsigned_type base = (1 << half_bits);  
```

---

## Comment 16

> Username: awulkiew  
> Created_at: 2015-05-20 16:56:01 UTC  
> Updated_at: 2015-05-20 17:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/297#discussion_r30723490  

Indeed, initially I implemented the pow function because I was using the T type which could be signed type and I feared that bit operations could cause an undefined behavior. But indeed according to the standard they should work as intended for non-negative values and unsigned types. At some point I switched to unsigned type so I could use bit shifts now. Furthermore I could now use bit shifts instead of division.  
  
EDIT: I've changed it as you suggested (https://github.com/boostorg/geometry/commit/3c958e420011a9ab5c42c9f2eb0fe11f0b9cc413).

---
