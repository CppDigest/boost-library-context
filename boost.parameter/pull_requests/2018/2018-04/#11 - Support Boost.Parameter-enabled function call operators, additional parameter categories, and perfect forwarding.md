# #11 Support Boost.Parameter-enabled function call operators, additional parameter categories, and perfect forwarding [Closed]

> Username: CromwellEnage  
> Created at: 2018-04-01 02:09:12 UTC  
> Updated at: 2018-04-09 21:29:52 UTC  
> Closed at: 2018-04-09 21:29:52 UTC  
> Url: https://github.com/boostorg/parameter/pull/11  

1.  Added code generation macros BOOST_PARAMETER_FUNCTION_CALL_OPERATOR and BOOST_PARAMETER_CONST_FUNCTION_CALL_OPERATOR to <boost/parameter/preprocessor.hpp>.  Both BOOST_PARAMETER_MEMBER_FUNCTION and BOOST_PARAMETER_CONST_MEMBER_FUNCTION concatenate the name parameter with other text to form internal name-ids as part of their expansions, so they cannot accept operator().  With the new macros, Boost.Parameter-enabled function call operators are now possible.  See "test/preprocessor_eval_category.cpp" for example usage.  
  
2.  Added parameter category qualifiers "consume", "move_from", and "forward" (current qualifiers are "in", "out", and "in_out") based on <http://www.modernescpp.com/index.php/c-core-guidelines-how-to-pass-function-parameters>.  Added new usage syntax BOOST_PARAMETER_NAME((object-name), namespace-name) qualifier(tag-name)) and BOOST_PARAMETER_NAME(qualifier(name)).  (Existing code that uses qualifiers directly and correctly with BOOST_PARAMETER_FUNCTION and other code generation macros should remain unaffected for now, so no breaking changes.)  The reason for the change in usage is to enable applying of parameter category constraints to Boost.Parameter-enabled functions and constructors invoked through argument composition.  (Otherwise, it is currently possible to use argument composition to bypass parameter category constraints applied in BOOST_PARAMETER_FUNCTION et. al.)  See "test/compose.cpp" for example usage.  
  
3.  The end of section 3.2.1 of the current home page tutorial notes "that because of the forwarding problem, parameter::parameters::operator() can't accept non-const rvalues."  Added code to eliminate this problem.  As a positive side effect, Boost.Parameter-enabled functions and constructors are no longer bound by BOOST_PARAMETER_MAX_ARITY on compilers that support perfect forwarding.  User code can now check for this support by detecting the configuration macro BOOST_PARAMETER_HAS_PERFECT_FORWARDING, or manually turn off this support by defining the configuration macro BOOST_PARAMETER_DISABLE_PERFECT_FORWARDING.  See "test/evaluate_category.cpp" and "test/preprocessor_eval_category.cpp" for example usage.  
  
4. It is no longer necessary to wrap boost::ref() around non-const lvalues, although this feature is still supported.  See "test/compose.cpp" and "test/evaluate_category.cpp".  
  
5. Updated documentation to reflect the above changes.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-04-01 13:52:50 UTC  
> Url: https://github.com/boostorg/parameter/pull/11#issuecomment-377788375  

Your jamfile changes are incorrect, in particular the 'compile' usage.

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-04-06 13:02:58 UTC  
> Url: https://github.com/boostorg/parameter/pull/11#issuecomment-379246469  

Since you have been making changes nearly every day, I would like to close this and have you resubmit it when you are ready with the PR. When you are ready with it I will take a look at it. Does that seem fair to you ? I appreciate your work with parameter in order to make it easier to use. But I will need some time to look at it all and would rather do it when you are ready to submit a final PR with which you are satisfied that it meets your goals.

---

## Comment 3

> Username: CromwellEnage  
> Created_at: 2018-04-06 18:46:16 UTC  
> Updated_at: 2018-04-06 20:52:38 UTC  
> Url: https://github.com/boostorg/parameter/pull/11#issuecomment-379342696  

I concur with your decision, but before you close the PR, could you point  
me in the right direction as to how I can submit jobs to Appveyor & Travis  
for my own work?  I'm afraid I won't be able to meet my goals without that  
kind of capability.  
  
Thanks for your attention!  
Cromwell D. Enage

---

## Comment 4

> Username: eldiener  
> Created_at: 2018-04-06 22:15:56 UTC  
> Url: https://github.com/boostorg/parameter/pull/11#issuecomment-379399334  

Since you have your own Github account you should be able to sign up for Travis and Appveyor and use your own forks of Boost libraries, including Parameter, to test your changes.

---

## Comment 5

> Username: CromwellEnage  
> Created_at: 2018-04-07 01:04:00 UTC  
> Url: https://github.com/boostorg/parameter/pull/11#issuecomment-379421161  

Oh, okay.  I saw your post after pushing my last commit.  I'll use my own fork until I'm ready.

---
