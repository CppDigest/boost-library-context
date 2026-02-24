# #947 Don't hide types or functions when exceptions are disabled [Closed]

> Username: ams2990  
> Created at: 2023-02-10 04:19:51 UTC  
> Updated at: 2023-02-13 15:35:52 UTC  
> Closed at: 2023-02-13 15:35:51 UTC  
> Url: https://github.com/boostorg/math/pull/947  

This breaks other libraries that can be compiled with or without exceptions that include Boost, if those libraries use types that are only visible when Boost has exceptions on.  
  
This commit partially reverts https://github.com/boostorg/math/commit/926e34c55ce12923e78d13762e5194bab3f7c47f

---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2023-02-11 17:32:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/947#pullrequestreview-1294592437  

📁 include/boost/math/policies/error_handling.hpp

```diff
  24 | #include <stdexcept>
  25 | #include <boost/math/tools/throw_exception.hpp>
  27 |- #endif
```

> Username: mborland  
> Created_at: 2023-02-11 17:32:32 UTC  
> Updated_at: 2023-02-11 17:32:42 UTC  
> Url: https://github.com/boostorg/math/pull/947#discussion_r1103668716  

I'm not entirely sure this is correct because I believe some platforms that do not support exceptions likewise do not have the header `<stdexcept>`


---

## Comment 2

> Username: mborland  
> Created_at: 2023-02-11 17:33:44 UTC  
> Url: https://github.com/boostorg/math/pull/947#issuecomment-1426833223  

I'll defer to @jzmaddock and @ckormanyos for this one.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-02-11 18:04:38 UTC  
> Url: https://github.com/boostorg/math/pull/947#issuecomment-1426840529  

I'm not sure this is entirely correct either, BOOST_NO_EXCEPTIONS being set implies that we cannot use throw/try/catch without compiler errors at the very least.  I suspect no <stdexcept> on embedded platforms as well, but @ckormanyos will know more about that.  
  
@ams2990 : what was the actual situation which caused this?  As far as I know Boost.Config will only set BOOST_NO_EXCEPTIONS when exceptions really are not available.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2023-02-11 21:40:02 UTC  
> Url: https://github.com/boostorg/math/pull/947#issuecomment-1426881973  

> not sure this is entirely correct either, BOOST_NO_EXCEPTIONS being set implies that we cannot use throw/try/catch without compiler errors at the very least. I suspect no on embedded platforms as well...  
  
To be honest, all I really did for our recent work was compile on platforms having no RTTI and no exceptions. I did not actually try to trhow.  
  
Just looking at the scope of changed from @ams2990 makes me think we might need to take a bit more time to consider these details more carefully.  
  
> what was the actual situation which caused this?  
  
Yes, @ams2990 exact error situations are usually best when analyzing. Do you have an exact situation, compiler, predicament that lead to an unexpected or unusual phenomonon? Even embedded systems are OK. But if we could do more to reproduce the issues you mentione, that would help find the best configuration adjustments, if any are needed.  
  
If you could provide more details on a  use case, I could run it through some other embedded compilers with RTTI and exceptions off maybe, depending on the depth of other third party.  
  
Cc: @mborland and @jzmaddock

---

## Comment 5

> Username: ams2990  
> Created_at: 2023-02-12 01:25:40 UTC  
> Updated_at: 2023-02-12 01:27:26 UTC  
> Url: https://github.com/boostorg/math/pull/947#issuecomment-1426915370  

I've encountered two breakages:  
* [hypergeometric_pFq.hpp](http://github.com/boostorg/math/blob/4aac532a8865ab54789f9d776ce79206793fc616/include/boost/math/special_functions/hypergeometric_pFq.hpp#L168) in boost/math  
* [algebra_solver_powell.hpp](https://github.com/stan-dev/math/blob/v3.2.0/stan/math/rev/functor/algebra_solver_powell.hpp#L163) in stan/math  
  
I was compiling with clang-trunk for x86-64 with -fno-exceptions. Because exceptions were not available, the type was #ifdef'd away and the code attempting to reference the exception types failed to compile, e.g.  
  
```  
stan_math/stan/math/rev/mat/functor/algebra_solver.hpp:174:24: error: no member named 'evaluation_error' in namespace 'boost::math'  
    throw boost::math::evaluation_error(message.str());  
          ~~~~~~~~~~~~~^  
```  
  
On the platforms I've worked with, throwing exceptions "works" even when exceptions are nominally disabled -- when an exception is thrown, the program is terminated. I've seen this most commonly when an exception is thrown from the C++ standard library.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2023-02-12 11:51:51 UTC  
> Url: https://github.com/boostorg/math/pull/947#issuecomment-1427012612  

>On the platforms I've worked with, throwing exceptions "works" even when exceptions are nominally disabled -- when an exception is thrown, the program is terminated. I've seen this most commonly when an exception is thrown from the C++ standard library.  
  
I think that *may* have been true for some older GCC versions, but for all current versions you get a hard error if there are any occurrences of try, catch or throw in the program at all:  
  
```  
 error: exception handling disabled, use ‘-fexceptions’ to enable  
   13 |       throw 0;  
```  
  
So you basically can't do what you're trying to do.  
  
>[hypergeometric_pFq.hpp](https://github.com/boostorg/math/blob/4aac532a8865ab54789f9d776ce79206793fc616/include/boost/math/special_functions/hypergeometric_pFq.hpp#L168) in boost/math  
  
It's a cop out, but I don't have any means of implementing that functionality without exceptions, I'll double check that that code doesn't get accidentally included when exceptions are disabled.  
  
```  
stan_math/stan/math/rev/mat/functor/algebra_solver.hpp:174:24: error: no member named 'evaluation_error' in namespace 'boost::math'  
    throw boost::math::evaluation_error(message.str());  
          ~~~~~~~~~~~~~^  
```  
  
The lack of evaluation_error may have been the first error triggered, but the throw statement would fail too.  
  
More info here: https://gcc.gnu.org/onlinedocs/libstdc++/manual/using_exceptions.html

---

## Comment 7

> Username: ams2990  
> Created_at: 2023-02-12 22:32:01 UTC  
> Url: https://github.com/boostorg/math/pull/947#issuecomment-1427149806  

I learned from one of our toolchain maintainers that we have some truly disgusting hacks to make code with try/catch blocks compile under -fno-exceptions. So, it works...but it's completely understandable you're not writing code with this in mind.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2023-02-13 15:35:51 UTC  
> Url: https://github.com/boostorg/math/pull/947#issuecomment-1428152465  

Hypergeometric issue is now fixed, closing this one.

---
