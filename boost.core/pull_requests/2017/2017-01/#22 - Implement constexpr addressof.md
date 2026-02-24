# #22 Implement constexpr addressof [Merged]

> Username: glenfe  
> Created at: 2017-01-30 03:41:50 UTC  
> Updated at: 2017-02-02 20:36:37 UTC  
> Merged at: 2017-01-31 13:47:52 UTC  
> Closed at: 2017-01-31 13:47:52 UTC  
> Url: https://github.com/boostorg/core/pull/22  

Putting the pull request up for discussion: C++17 provides constexpr addressof, which we can provide in C++11 and above when specific language features are supported by the implementation (e.g. expression SFINAE and constexpr). Do we want this?  
  
I will add the MSVC workarounds back and update the pull request. The Sun and Borland workarounds don't have to be removed either.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-01-30 09:04:11 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276010074  

> Do we want this?  
  
Why not? Provided that compatibility with C++03 is preserved, I think `boost::addressof` should provide the same services as `std::addressof`.  
  
> I will add the MSVC workarounds back and update the pull request. The Sun and Borland workarounds don't have to be removed either.  
  
Also, `BOOST_FORCEINLINE` don't have to be removed.

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-01-30 13:09:49 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276058523  

I may come across as overly negative, but this sounds like lot of potential problems for zero upside.

---

## Comment 3

> Username: glenfe  
> Created_at: 2017-01-30 13:29:11 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276062453  

The use case that comes to mind is a conforming C++17 optional implementation which requires constexpr addressof (which is why libstdc++, libc++, Andrezj et al implemented much of the above).  
  
Two alternatives could be to provide it with a different name, e.g. constexpr_addressof, or require (via macro) explicitly choosing the above functionality.

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-01-30 14:11:15 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276071592  

On 01/30/17 16:29, Glen Fernandes wrote:  
> The use case that comes to mind is a conforming C++17 optional  
> implementation which requires constexpr addressof (which is why  
> libstdc++, libc++, Andrezj et al implemented much of the above).  
>  
> Two alternatives could be to provide it with a different name, e.g.  
> constexpr_addressof, or require (via macro) explicitly choosing the  
> above functionality.  
  
Why the different naming, let alone config macro? Is there a problem   
with implementing the universal `addressof`?

---

## Comment 5

> Username: glenfe  
> Created_at: 2017-01-30 14:25:25 UTC  
> Updated_at: 2017-01-30 21:18:54 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276074932  

Not that I know of. I was deferring to Peter's instincts for predicting trouble. I've used the above implementation (or close to it) for our implementation of optional without any issues for a while.

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-01-30 14:56:11 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276083227  

On 01/30/17 17:25, Glen Fernandes wrote:  
> Not that I know of (I was deferring to Peter's instincts for predicting  
> trouble). I've used the above implementation (or close to it) for our  
> implementation of optional without any issues for a while.  
  
I think the problems, if they appear, can be solved. That should not   
stop us from moving forward with `addressof`.

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-01-30 18:44:29 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276151853  

I updated .travis.yml to test on more compilers and more modes (C++03/11/14) as the current one was only a quick C++03 smoke check. This was fine for what we had but wasn't enough to test this change. The test time jumped from a few minutes to a few hours but what could one do.  
  
Glen, would you please merge the .travis.yml change and push the branch again to kick off Travis?

---

## Comment 8

> Username: apolukhin  
> Created_at: 2017-01-30 19:52:53 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276171096  

You could also use the feature testing macro `__cpp_lib_array_constexpr` to detect `constexpr std::addressof` availability ([p0031r0](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0031r0.html), that adds the feature testing macro, can not be implemented without `constexpr std::addressof`).  
  
So if `__cpp_lib_array_constexpr` defined - just use `std::addressof` inside the `boost::addressof`.

---

## Comment 9

> Username: pdimov  
> Created_at: 2017-01-30 20:06:10 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276174800  

If we already have a working boost::addressof implementation, it's not clear to me why we'd need to use std::addressof instead (when it's present and constexpr).

---

## Comment 10

> Username: glenfe  
> Created_at: 2017-01-30 21:16:21 UTC  
> Updated_at: 2017-01-30 21:20:08 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276193072  

Peter, will do.

---

## Comment 11

> Username: K-ballo  
> Created_at: 2017-01-31 00:34:49 UTC  
> Updated_at: 2017-01-31 00:35:08 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276237683  

> > Two alternatives could be to provide it with a different name, e.g.  
> > constexpr_addressof, or require (via macro) explicitly choosing the  
> > above functionality.  
>  
> Why the different naming, let alone config macro? Is there a problem  
> with implementing the universal `addressof`?  
  
The universal `addressof` cannot be implemented in plain C++, it requires a builtin.

---

## Comment 12

> Username: glenfe  
> Created_at: 2017-01-31 00:41:11 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276238701  

The implementations are starting to provide builtins, that Agustín pointed out. We should use them for addressof (once we've verified they pass all tests). For older compilers without the C++17 builtin, the question still remains as to whether to provide the emulated functionality as addressof or under a new name (e.g. constexpr_addressof).  
  
One advantage of the latter could be as simple (but no less important) as not burdening the users with the compile-time overhead of the expression SFINAE detection, which I realize isn't an unreasonable desire.

---

## Comment 13

> Username: glenfe  
> Created_at: 2017-01-31 01:06:07 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276242874  

A note about the `defined(BOOST_MSVC)` check: It is because `BOOST_NO_SFINAE_EXPR` is not defined for VC14 Update 3. If VC15 eventually supports these cases by improving its SFINAE support, it can be relaxed to a `(BOOST_MSVC < 1910)` check.

---

## Comment 14

> Username: glenfe  
> Created_at: 2017-01-31 05:42:51 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276280222  

Builds passed with all compilers. Tomorrow I'll update the pull request with usage of `__builtin_addressof` on implementations which support it.

---

## Comment 15

> Username: pdimov  
> Created_at: 2017-01-31 13:09:31 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276358665  

Looks good to me.  
  
Re `defined(BOOST_MSVC)`, it should be `defined(BOOST_MSVC) && BOOST_WORKAROUND(BOOST_MSVC, BOOST_TESTED_AT(1900))`. For stylistic reasons you could separate it in its own `#if` block and add a comment. (The idea of using `BOOST_WORKAROUND` is to allow compiler vendors to switch the workarounds off and see if their new version, VC15 in this case, works.)  
  
Similarly, the `defined(__INTEL_COMPILER)` workaround should also use `BOOST_WORKAROUND`.  
  
I'm a bit concerned about using generic names such as `make_void` here but don't see an immediate problem with that.  
  
You can merge when ready as far as I'm concerned.

---

## Comment 16

> Username: Lastique  
> Created_at: 2017-02-02 15:46:06 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-276993952  

gcc 4.7 failed some tests after this change:  
  
`../boost/core/addressof.hpp:235:44: error: 'operator&' not defined`  
  
http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dg4-7-Docker-64on64-boost-bin-v2-libs-atomic-test-atomicity-test-gcc-4-7~c++11-debug-threading-multi.html  
  
http://www.boost.org/development/tests/develop/developer/output/Sandia-gcc-4-7-2-c++11-boost-bin-v2-libs-atomic-test-atomicity-test-gcc-4-7-2-debug-threading-multi.html

---

## Comment 17

> Username: pdimov  
> Created_at: 2017-02-02 16:21:28 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-277004727  

Looks like our `reference_wrapper` tests don't test with `volatile`. I'll add a quick test for that.  
  
Also, I'm getting lots of  
  
```  
1>c:\Projects\boost-git\boost\boost/core/addressof.hpp(44) : warning C4512: 'boost::core::detail::addressof_ref<T>' : assignment operator could not be generated  
```  
  
from MSVC.

---

## Comment 18

> Username: glenfe  
> Created_at: 2017-02-02 16:27:29 UTC  
> Updated_at: 2017-02-02 17:19:55 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-277006506  

Looks like gcc 4.8+ fixed those issues; **[edit:** I'll find a workaround for 4.7.**--end-edit]** I had removed the `operator=` declaration because I didn't think it was necessary; I'll add it back to suppress the warnings.

---

## Comment 19

> Username: pdimov  
> Created_at: 2017-02-02 16:43:12 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-277011194  

Hmm, `addressof_test` already tests with `volatile`, why isn't this triggering the error on 4.7? Odd.

---

## Comment 20

> Username: glenfe  
> Created_at: 2017-02-02 17:04:10 UTC  
> Updated_at: 2017-02-02 18:13:37 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-277017459  

Actually, we can support 4.7 too if we enable my Intel workaround for it.  i.e. The presence of the following:  
```  
#if BOOST_WORKAROUND(BOOST_INTEL, < 1600)  
struct addressof_addressable { };  
  
addressof_addressable*   
operator&(addressof_addressable&) BOOST_NOEXCEPT;  
#endif  
```  
  
The condition just needs to be:  
```  
#if BOOST_WORKAROUND(BOOST_INTEL, < 1600) || BOOST_WORKAROUND(BOOST_GCC, < 40800)  
```

---

## Comment 21

> Username: glenfe  
> Created_at: 2017-02-02 17:10:03 UTC  
> Updated_at: 2017-02-02 17:10:24 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-277019163  

Also, the `#if defined(macro) && BOOST_WORKAROUND(macro, check)` can just be expressed more succinctly as `#if BOOST_WORKAROUND(macro, check)`, right? Is there any reason to prefer the former?

---

## Comment 22

> Username: pdimov  
> Created_at: 2017-02-02 17:19:05 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-277021687  

The usual - warning suppression when the macro isn't defined (`-Wundef`).

---

## Comment 23

> Username: pdimov  
> Created_at: 2017-02-02 17:23:09 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-277022918  

Although going by https://svn.boost.org/trac/boost/ticket/1130 and the referenced https://svn.boost.org/trac/boost/changeset/39473/trunk/boost/detail/workaround.hpp, `BOOST_MSVC` and `BOOST_INTEL` are special-cased to not warn, so maybe I'm behind the times on this one. :-)

---

## Comment 24

> Username: pdimov  
> Created_at: 2017-02-02 17:26:52 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-277023985  

Nine years behind, to be specific.

---

## Comment 25

> Username: pdimov  
> Created_at: 2017-02-02 18:05:32 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-277034568  

> Hmm,  `addressof_test` already tests with `volatile`, why isn't this triggering the error on 4.7? Odd.  
  
Because the Travis matrix didn't have g++ 4.7 in C++11 mode. Mystery solved, I guess. Added.

---

## Comment 26

> Username: pdimov  
> Created_at: 2017-02-02 19:51:15 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-277063432  

Still doesn't work with a the private `operator&` member.

---

## Comment 27

> Username: glenfe  
> Created_at: 2017-02-02 20:24:18 UTC  
> Updated_at: 2017-02-02 20:36:37 UTC  
> Url: https://github.com/boostorg/core/pull/22#issuecomment-277072548  

Affirmative. If I don't find a 4.7 workaround for the private member case tonight, I'll exclude it and limit the functionality to 4.8 and higher.

---
