# #30 Test function type template parameter support [Merged]

> Username: CromwellEnage  
> Created at: 2018-11-17 20:43:04 UTC  
> Updated at: 2018-12-22 10:34:08 UTC  
> Merged at: 2018-11-25 22:57:42 UTC  
> Closed at: 2018-11-25 22:57:42 UTC  
> Url: https://github.com/boostorg/signals2/pull/30  

<boost/signals2/variadic_signal.hpp>  
* Add boost::signals2::detail::variadic_extended_signature template specializations for boost::function and std::function (when available).  
  
<boost/signals2/signal_type.hpp>  
* Leverage Boost.Parameter support for function type template parameters.  
  
"test/signal_type_test.cpp"  
* Remove BOOST_SIGNALS2_NAMED_SIGNATURE_PARAMETER preprocessor guard.  
  
"test/Jamfile.v2"  
* Define BOOST_PARAMETER_EXPONENTIAL_OVERLOAD_THRESHOLD_ARITY=0 where applicable to prevent ICE failures.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-11-17 21:59:17 UTC  
> Updated_at: 2018-11-22 09:12:55 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-439650619  

# [Codecov](https://codecov.io/gh/boostorg/signals2/pull/30?src=pr&el=h1) Report  
> Merging [#30](https://codecov.io/gh/boostorg/signals2/pull/30?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/signals2/commit/0c51b5dcbf5961054447ce83f467e6e99db16c54?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/signals2/pull/30/graphs/tree.svg?width=650&token=s6LljlwIKV&height=150&src=pr)](https://codecov.io/gh/boostorg/signals2/pull/30?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #30   +/-   ##  
========================================  
  Coverage    53.13%   53.13%             
========================================  
  Files           29       29             
  Lines          862      862             
  Branches       337      337             
========================================  
  Hits           458      458             
  Misses          91       91             
  Partials       313      313  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/signals2/pull/30?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/signals2/pull/30?src=pr&el=footer). Last update [0c51b5d...dbee9ee](https://codecov.io/gh/boostorg/signals2/pull/30?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: fmhess  
> Created_at: 2018-11-19 22:32:36 UTC  
> Updated_at: 2018-11-19 22:34:08 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-440066109  

I don't fully understand your patch yet, but from looking at it I have a couple concerns.  
  
1) Whatever BOOST_PARAMETER_EXPONENTIAL_OVERLOAD_THRESHOLD_ARITY does, the commit message implies the tests will produce ICE if it is not defined?  If the tests need it, won't user code also produce ICE with your patch?  
  
2) What are the `"<preserve-target-tests>off"` changes doing in your patch, and in any case did you mean "preserve-test-targets"?  
  
3) What are you doing to variadic_extended_signature?  That is supposed to generate a boost::function with a modified signature that has an extra connection parameter which can be used as the default for the ExtendedSlotFunction template parameter.

---

## Comment 3

> Username: CromwellEnage  
> Created_at: 2018-11-20 03:33:50 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-440125762  

1.  *sigh*  The original intent of BOOST_PARAMETER_EXPONENTIAL_OVERLOAD_THRESHOLD_ARITY being defined as a non-zero integer (and therefore allowing boost::parameter::parameters to define an exponential number of operator() overloads up to the specified arity for compilers that do not support perfect forwarding) was to free the user from having to wrap boost::ref() or std::ref() around non-const lvalues by default.  But now I see this is causing more problems downstream.  So, I'll define BOOST_PARAMETER_EXPONENTIAL_OVERLOAD_THRESHOLD_ARITY as zero by default for the next PR to Boost.Parameter.  If and when that gets accepted and merged, I'll get rid of BOOST_PARAMETER_EXPONENTIAL_OVERLOAD_THRESHOLD_ARITY from the jamfiles.  
2. I set "<preserve-target-tests>off" because past tests would stop in error due to running out of hard disk space allotted to them, so as a precaution I configured the test script to delete each executable after it was run.  
3. I didn't notice the extra connection parameter at first, my fault.  The next commit will fix this.

---

## Comment 4

> Username: CromwellEnage  
> Created_at: 2018-11-20 21:13:01 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-440431312  

Okay, I've reverted "test/Jamfile.v2".

---

## Comment 5

> Username: CromwellEnage  
> Created_at: 2018-11-20 22:12:20 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-440447912  

Now, to explain my changes:  
  
If the type passed in to boost::parameter::template_keyword is a function type, then boost::parameter::template_keyword will define its value type to be either a boost::function or a std::function wrapped around that type.  In this patch, the job of the additional helper metafunctions and metafunction overloads is to unwrap the boost::function or std::function in order to get at the original function type, its return type, and its arguments so that the rest of the code works as usual.

---

## Comment 6

> Username: fmhess  
> Created_at: 2018-11-20 22:16:13 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-440448917  

Thanks for the explanation.  By the way, are you aware of  
  
https://svn.boost.org/trac10/ticket/2793  
  
Is it possible for you to just fix boost.parameter to handle function types?

---

## Comment 7

> Username: CromwellEnage  
> Created_at: 2018-11-20 23:23:24 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-440465168  

I remember the Trac ticket.  It was my starting point for the changes I made to boost::parameter::template_keyword.  (BTW, I renamed your program to function_type_tpl_param.cpp and expanded on it.)  I remember repeatedly trying and failing to get boost::parameter::template_keyword to store function types directly without wrapping boost::function or std::function around them.

---

## Comment 8

> Username: fmhess  
> Created_at: 2018-11-21 05:39:05 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-440537861  

Ok, starting from git develop, I just modified aux_/template_keyword.hpp so it defines value_type and reference as was done in David Abraham's original patch from the trac ticket (rather than wrapping function types in a std::function as in your recent change).  Then I defined BOOST_SIGNALS2_NAMED_SIGNATURE_PARAMETER in the signals2 jamfile for the signal_type_test test.  And it works!  
  
Now I know I didn't think David's patch was correct originally.  But re-reading it, I objected because my test failed.  But reading the comments in his patch, it looks like my test just failed because I was using "binding" instead of "value_type".  So now I'm thinking the comment by expaler in the trac ticket was right, and the original Abrahams patch should be applied.  
  
What do you think?  
  
Oh, also I like the part of your patch which updates the max arity define, since signals2 doesn't need to bump up the default value any more.

---

## Comment 9

> Username: CromwellEnage  
> Created_at: 2018-11-21 06:48:49 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-440556756  

Huh, how about that?  My instincts were right back then.  (I'm expaler, BTW.)  I'll see about doing what you did with boost::parameter::template_keyword and then updating test/function_type_tpl_param.cpp accordingly.

---

## Comment 10

> Username: CromwellEnage  
> Created_at: 2018-11-21 17:02:26 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-440739770  

Okay, I've submitted a PR to Boost.Parameter that applies a modified version of David Abrahams' patch.  If and when it is accepted and merged, I'll reduce the changes to defining BOOST_SIGNALS2_NAMED_SIGNATURE_PARAMETER.  Do you want it defined in <boost/signals2/signal_type.hpp> or in "test/Jamfile.v2"?

---

## Comment 11

> Username: fmhess  
> Created_at: 2018-11-21 20:37:59 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-440801500  

In signal_type.hpp.  Or you can remove the macro entirely, I only added it  
as a stopgap until boost.parameter got fixed.

---

## Comment 12

> Username: CromwellEnage  
> Created_at: 2018-11-22 05:19:53 UTC  
> Url: https://github.com/boostorg/signals2/pull/30#issuecomment-440915816  

The other PR got merged, so now all my patch does is remove BOOST_SIGNALS2_NAMED_SIGNATURE_PARAMETER, update usage of BOOST_PARAMETER_MAX_ARITY, and replace inclusion of <boost/parameter.hpp> with inclusion of the minimal necessary subset of Boost.Parameter headers.

---
