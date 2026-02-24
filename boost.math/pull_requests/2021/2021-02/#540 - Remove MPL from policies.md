# #540 Remove MPL from policies [Merged]

> Username: mborland  
> Created at: 2021-02-15 13:09:39 UTC  
> Updated at: 2021-02-25 16:43:32 UTC  
> Merged at: 2021-02-21 18:44:31 UTC  
> Closed at: 2021-02-21 18:44:31 UTC  
> Url: https://github.com/boostorg/math/pull/540  

Also removes dependencies from boost types (e.g. boost/assert and boost/type_traits) that were implemented in C++11.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-02-15 19:37:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/540#pullrequestreview-590718434  

📁 include/boost/math/policies/policy.hpp

```diff
 845 |+ inline constexpr int digits_imp(std::true_type const&) noexcept
 846 | {
 847 | #ifndef BOOST_NO_LIMITS_COMPILE_TIME_CONSTANTS
```

> Username: jzmaddock  
> Created_at: 2021-02-15 19:37:04 UTC  
> Updated_at: 2021-02-19 17:07:25 UTC  
> Url: https://github.com/boostorg/math/pull/540#discussion_r576391307  

Just a heads up that BOOST_NO_LIMITS_COMPILE_TIME_CONSTANTS can be assumed to NOT be set post C++11, so these pp-branches can be chopped down.


---

## Comment 2

> Username: mborland  
> Created_at: 2021-02-19 12:09:50 UTC  
> Url: https://github.com/boostorg/math/pull/540#issuecomment-782036178  

@jzmaddock The current failures are all related to multiprecision gmp and mpfr related using older versions of clang (5-8) on Ubuntu. They are all similar to:  
  
```  
In file included from ../tools/bessel_derivative_data.cpp:14:  
In file included from ../../../boost/multiprecision/mpfr.hpp:11:  
../../../boost/multiprecision/gmp.hpp:291:11: error: no template named '_mp_size'; did you mean 'mp11::mp_size'?  
      if (mpf_sgn(m_data) == 0)  
          ^  
/usr/include/x86_64-linux-gnu/gmp.h:2239:26: note: expanded from macro 'mpf_sgn'  
#define mpf_sgn(F) ((F)->_mp_size < 0 ? -1 : (F)->_mp_size > 0)  
                         ^  
../../../boost/mp11/list.hpp:45:19: note: 'mp11::mp_size' declared here  
67  
template<class L> using mp_size = typename detail::mp_size_impl<L>::type;  
```  
  
Is this a known issue? I don't expect naming collision because the majority of tests pass.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-02-19 13:23:38 UTC  
> Url: https://github.com/boostorg/math/pull/540#issuecomment-782071372  

Someone else has experienced this: https://stackoverflow.com/questions/57857572/why-does-boost-log-break-boost-gmp-multiprecision, which leads on to this clang bug: https://bugs.llvm.org/show_bug.cgi?id=43266  
  
Not quite sure what we do about though :(

---

## Comment 4

> Username: mborland  
> Created_at: 2021-02-19 14:09:08 UTC  
> Url: https://github.com/boostorg/math/pull/540#issuecomment-782098120  

I guess I should have looked at SO before asking you. Good news is now that I replaced MP11 with internal tools the error seems to have gone away.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-02-19 17:45:39 UTC  
> Url: https://github.com/boostorg/math/pull/540#issuecomment-782230996  

I should have said this before, but the purpose of the main meta-program in policies.hpp is to take a random policy and normalise it in some way so that if a function is instantiated with 2 different policy types, that actually specify the same thing, then internally we call the implementation level functions with the same policy type (ie code bloat suppression).    
  
The point is, it doesn't really matter how that is achieved, or what the normalised form is, as long as there is one.  
  
Currently we:  
  
* Remove any policy that is the same as the default, and then  
* Sort whatever is left.  
  
So there may well be a simpler/neater way to do this without re-implementing the MPL-like machinery.

---

## Comment 6

> Username: mborland  
> Created_at: 2021-02-19 18:16:52 UTC  
> Url: https://github.com/boostorg/math/pull/540#issuecomment-782249532  

The structure of the program made sense, and has been around for quite some time so I did not change a whole lot in that respect. Not all of the stuff in the new meta-programming tools header is needed so I can pare it back to the minimum if you want. I thought it would be good to have to avoid re-introducing MPL or MP11 down the road. I believe this is very close to going green. These last few commits have been odd failures on old compilers.

---

## Comment 7

> Username: mborland  
> Created_at: 2021-02-19 19:31:44 UTC  
> Url: https://github.com/boostorg/math/pull/540#issuecomment-782293806  

This is finally green. Pending any further review it should be good to go.

---

## Comment 8

> Username: mborland  
> Created_at: 2021-02-21 16:49:03 UTC  
> Url: https://github.com/boostorg/math/pull/540#issuecomment-782887362  

@jzmaddock Any changes required on this one?

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2021-02-21 18:44:25 UTC  
> Url: https://github.com/boostorg/math/pull/540#issuecomment-782905195  

There's a couple of sanity checks I want to do on the new policy code, but I'll merge, and file a bug report if I find anything.  
  
Once again, many thanks for this!

---
