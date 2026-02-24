# #25 Support perfect forwarding with C++03 workarounds [Closed]

> Username: CromwellEnage  
> Created at: 2018-11-06 17:37:29 UTC  
> Updated at: 2018-11-07 15:19:11 UTC  
> Closed at: 2018-11-07 15:19:11 UTC  
> Url: https://github.com/boostorg/parameter/pull/25  

1. The end of section 3.2.1 of the current home page tutorial notes "that because of the forwarding problem, parameter::parameters::operator() can't accept non-const rvalues."  Add code to eliminate this problem.  As a positive side effect, Boost.Parameter-enabled functions and constructors are no longer bound by BOOST_PARAMETER_MAX_ARITY on compilers that support perfect forwarding. User code can now check for this support by detecting the configuration macro BOOST_PARAMETER_HAS_PERFECT_FORWARDING.  See "test/evaluate_category.cpp" and "test/preprocessor_eval_category.cpp" for example usage.  
2. Add parameter category qualifiers "consume" and "move_from" (current qualifiers are "in", "out", "in_out", and "forward") based on http://www.modernescpp.com/index.php/c-core-guidelines-how-to-pass-function-parameters.  
3. Update documentation to reflect the above changes.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-11-06 19:51:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/25#issuecomment-436385690  

I'd love to see code coverage numbers before and after this change, but the CI for this repository doesn't do it.  A delta of roughly 15k lines sure is a lot!

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-11-07 13:23:51 UTC  
> Url: https://github.com/boostorg/parameter/pull/25#issuecomment-436620988  

Please fix conflicts.

---
