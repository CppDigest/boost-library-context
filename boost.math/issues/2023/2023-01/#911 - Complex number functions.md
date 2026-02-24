# #911 - Complex number functions [Closed]

> Username: evanmiller  
> Created at: 2023-01-02 23:51:39 UTC  
> Updated at: 2023-01-12 11:35:23 UTC  
> Closed at: 2023-01-12 11:35:23 UTC  
> Url: https://github.com/boostorg/math/issues/911  

Are these still needed?  
  
https://www.boost.org/doc/libs/1_81_0/libs/math/doc/html/inverse_complex.html  
  
I see that all six of these functions are in [std::complex](https://en.cppreference.com/w/cpp/numeric/complex) as of C++11.

---

## Comment 1

> Username: mborland  
> Created at: 2023-01-09 15:15:09 UTC  
> Url: https://github.com/boostorg/math/issues/911#issuecomment-1375775062  

Since we are moving to C++14 in the next release we could add the `[[deprecated("Replaced by C++11")]]` attribute to these.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-01-09 16:45:45 UTC  
> Url: https://github.com/boostorg/math/issues/911#issuecomment-1375933458  

I'm cool with that.

---

## Comment 3

> Username: NAThompson  
> Created at: 2023-01-09 17:23:18 UTC  
> Url: https://github.com/boostorg/math/issues/911#issuecomment-1375985893  

Are we sure that 1) these don't work with multiprecision, and 2) they aren't used by some standard library implementation?

---

## Comment 4

> Username: mborland  
> Created at: 2023-01-09 17:27:07 UTC  
> Url: https://github.com/boostorg/math/issues/911#issuecomment-1375990632  

> Are we sure that 1) these don't work with multiprecision, and 2) they aren't used by some standard library implementation?  
  
1) They return std::complex<T> which is limited to `float`, `double`, and `long double`  
  
2) That would be a question for @CaseyCarter or @StephanTLavavej. Does MSVC use Boost.Math for complex special functions? I know you use some of the others.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2023-01-09 18:08:48 UTC  
> Updated at: 2023-01-09 18:09:32 UTC  
> Url: https://github.com/boostorg/math/issues/911#issuecomment-1376041773  

> work with multiprecision  
  
When I was recently writing my new `cpp_double_fp_backend` for Multiprecision, I got the impression that these functions were used in some of the tests. I'm not sure if they are actually needed for the Multiprecision-backend implementations themselves.  
  
I'll try to take a look before the trail runs cold, since I was recently just snoping around these areas.

---

## Comment 6

> Username: StephanTLavavej  
> Created at: 2023-01-09 19:45:45 UTC  
> Url: https://github.com/boostorg/math/issues/911#issuecomment-1376188297  

We don't use Boost.Math for `<complex>`'s inverse trig functions; our use is restricted to `[sf.cmath]`.

---

## Comment 7

> Username: ckormanyos  
> Created at: 2023-01-10 17:59:09 UTC  
> Url: https://github.com/boostorg/math/issues/911#issuecomment-1377644417  

> We don't use Boost.Math for `<complex>`'s inverse trig functions; our use is restricted to `[sf.cmath]`.  
  
Cool

---

## Comment 8

> Username: ckormanyos  
> Created at: 2023-01-10 18:00:57 UTC  
> Updated at: 2023-01-10 18:02:42 UTC  
> Url: https://github.com/boostorg/math/issues/911#issuecomment-1377646667  

> Are we sure that 1) these don't work with multiprecision  
  
No, personally I'm really unsure of that.  
  
Just about every floating-point backend [includes the headers](https://github.com/boostorg/multiprecision/blob/8de45037b62b3405139d73d7a14acf0831bf63b6/include/boost/multiprecision/cpp_bin_float.hpp#L30) when Boost.Math is available. If this is absolutely necessary, I'm not sure.  
  
Wouldn't we need to modify these backends in Multiprecision if deprecation ensues? or did i miss the obvious, as I may have and sometimes do?

---

## Comment 9

> Username: jzmaddock  
> Created at: 2023-01-10 18:33:10 UTC  
> Url: https://github.com/boostorg/math/issues/911#issuecomment-1377683213  

No std::complex can not be used for multiprecision types.  But... the scalar versions for float/double/long double could well be deprecated I guess.  Does that make things worse/less generic though?
