# #241 - lgamma slow with defaults [Open]

> Username: wds15  
> Created at: 2019-08-13 06:21:16 UTC  
> Updated at: 2019-08-13 11:58:57 UTC  
> Url: https://github.com/boostorg/math/issues/241  

The `boost::math::lgamma` implementation is about 2-3x slower than the `std::lgamma` implementation when used with standard `double`s.  
  
When turning off the do the propagation from `double` to `long double` the boost implementation is comparable in speed to `std::lgamma`, but still 10-20% slower.  
  
However, when turning off propagation to `long double` problems with early overflow are the price to pay... see the next issue.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-08-13 09:14:53 UTC  
> Url: https://github.com/boostorg/math/issues/241#issuecomment-520756962  

We need to look at this - when that function was written (and the library started), there was negligible performance difference between double and the old x87 long double, so it was worth using internal promotion for accuracy.  However, as you rightly point out, things have moved on and there is now a very significant difference between x64 floating point and x87 code.  
  
It's a one line change to change the default, but it would break all our tests as the expected errors are predicated on internal promotion taking place.  
  
What does everyone else feel about this as a breaking change?

---

## Comment 2

> Username: wds15  
> Created at: 2019-08-13 09:26:58 UTC  
> Url: https://github.com/boostorg/math/issues/241#issuecomment-520761059  

If your thought is to use `std::lgamma`... then that is not really an option. The `std::lgamma` has issues with threaded programs where the sign of the result is stored in a global `signgam` variable, which is locked on macOS during the function call leading to terrible performance. You could use the `lgamma_r` function which is reentrant safe (available by defining `_REENTRANT` before including `cmath`)... but this is not a C++ standard.  
  
(performance issues with threaded programs using `std::lgamma` made me investigate all this)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-08-13 10:46:38 UTC  
> Url: https://github.com/boostorg/math/issues/241#issuecomment-520785338  

>If your thought is to use std::lgamma  
  
No there's too much variation between platforms IMO.

---

## Comment 4

> Username: NAThompson  
> Created at: 2019-08-13 11:48:19 UTC  
> Url: https://github.com/boostorg/math/issues/241#issuecomment-520802406  

> What does everyone else feel about this as a breaking change?  
  
I think it should be done, and there should be a slow but steady commitment to doing the same with every function in the library. I don't think the breaking change will really affect many people-half ULP accuracy which can be achieved with long double promotion is not an expectation many people have.

---

## Comment 5

> Username: wds15  
> Created at: 2019-08-13 11:58:57 UTC  
> Url: https://github.com/boostorg/math/issues/241#issuecomment-520805315  

uh... I misread... you are suggesting to change the default policy to avoid the promotion. Sounds good to me given that functions still work fine.  
  
(FYI, the digamma functions runs about 2x faster)
