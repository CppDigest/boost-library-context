# #635 - Is boost::multiprecision::float128 a trivial type? [Closed]

> Username: nmnobre  
> Created at: 2024-07-30 13:37:10 UTC  
> Updated at: 2024-08-06 17:36:23 UTC  
> Closed at: 2024-08-06 17:36:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/635  

Hi,  
  
Could you please post the code you used to generate the following?  
  
https://github.com/boostorg/multiprecision/blob/0f6a276756e2fb3b9226000a3b24f788787e2474/example/float128_snips.cpp#L50-L90  
  
For some reason, on my machine, `std::is_trivial<float128>::value` is `false`?  
  
Thank you,  
Nuno

---

## Comment 1

> Username: ckormanyos  
> Created at: 2024-07-30 14:11:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/635#issuecomment-2258456104  

> on my machine, `std::is_trivial<float128>::value` is `false`?  
  
And I believe on every other machine. This is a software type with a wrapper and it is not trivial.  
  
I hope I got that right, as @jzmaddock can correct me if I'm wrong.

---

## Comment 2

> Username: nmnobre  
> Created at: 2024-07-30 14:14:06 UTC  
> Updated at: 2024-07-30 14:14:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/635#issuecomment-2258461495  

Agreed. But the output I quoted clearly states `std::is_trivial<> = true`?

---

## Comment 3

> Username: mborland  
> Created at: 2024-07-30 15:21:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/635#issuecomment-2258616013  

`boost::multiprecision::float128` should be a trivial type since it's just a wrapper over `__float128`, but godbolt confirms for a range of library versions and compilers it is not: https://godbolt.org/z/PPxa5K5nP. Since it shows type name of `float128_t` in the above snip that may be from `__float128` rather than `boost::multiprecision::float128`.

---

## Comment 4

> Username: nmnobre  
> Created at: 2024-07-30 15:27:47 UTC  
> Updated at: 2024-07-30 15:33:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/635#issuecomment-2258629842  

That's exactly my experience as well, thus why I asked for the actual code. :)

---

## Comment 5

> Username: mborland  
> Created at: 2024-07-30 15:48:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/635#issuecomment-2258671851  

> That's exactly my experience as well, thus why I asked for the actual code. :)  
  
I doubt the code still exists since it's not in the repo. The comment in question was added here: https://github.com/boostorg/multiprecision/commit/0fe173730d5e74a376a9e1f080e3a0d801e7bd11, but unfortunately the author of that commit passed a few years ago. I tried Godbolt with the configuration specified in the comment, and it did not show `boost::multiprecision::float128` as trivial then either: https://godbolt.org/z/qddTnq8qz

---

## Comment 6

> Username: nmnobre  
> Created at: 2024-07-30 16:01:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/635#issuecomment-2258697321  

Oh, I just noticed `is_fundamental` is also marked `true`... I think you might be right, that output isn't representative of boost's wrapper.

---

## Comment 7

> Username: nmnobre  
> Created at: 2024-07-30 16:10:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/635#issuecomment-2258715249  

The copy constructor here is non-trivial, so I think that's the end of that:  
  
https://github.com/boostorg/multiprecision/blob/0f6a276756e2fb3b9226000a3b24f788787e2474/include/boost/multiprecision/number.hpp#L54

---

## Comment 8

> Username: jzmaddock  
> Created at: 2024-08-01 18:28:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/635#issuecomment-2263704187  

I think that output came from something else entirely - we should remove or update it.  
  
I can't help wondering if we *could* make the type trivial though, it probably *should* be.
