# #128 - Make use of the Boost.Config/Boost.Predef and cleanup macros [Open]

> Username: viboes  
> Created at: 2016-03-06 18:50:20 UTC  
> Updated at: 2016-04-28 03:43:12 UTC  
> Url: https://github.com/boostorg/hof/issues/128  

I will suggest to start by using the existing configuration macros  
  
The examples I give are for Boost.Config  
  
BOOST_FIT_NO_EXPRESSION_SFINAE - BOOST_NO_SFINAE_EXPR  
BOOST_FIT_HAS_TEMPLATE_ALIAS - BOOST_NO_CXX11_TEMPLATE_ALIASES  
BOOST_FIT_HAS_RELAXED_CONSTEXPR - BOOST_NO_CXX14_CONSTEXPR  
  
There are others that don't exists yet (or I don't understand them to do the mapping :( ), and that could be proposed in the future if needed by two libraries (not now). Anyway a description of what the macro is detecting would be welcome.  
  
BOOST_FIT_NO_ORDERED_BRACE_INIT  
BOOST_FIT_NO_UNIQUE_STATIC_LAMBDA_FUNCTION_ADDR  
BOOST_FIT_NO_TYPE_PACK_EXPANSION_IN_TEMPLATE  
BOOST_FIT_NO_STD_DEFAULT_CONSTRUCTIBLE  
BOOST_FIT_NO_UNIQUE_STATIC_VAR  
  
BOOST_FIT_HAS_MANUAL_DEDUCTION  
BOOST_FIT_HAS_MANGLE_OVERLOAD  
BOOST_FIT_HAS_COMPLETE_DECLTYPE  
  
The following macros seems to need a clean up as they are defined explicitly  
# define BOOST_FIT_NO_CONSTEXPR_VOID 1  
# define BOOST_FIT_HAS_STATIC_LAMBDA 1  
  
The following are not used  
BOOST_FIT_HAS_RVALUE_THIS

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-11 17:11:26 UTC  
> Url: https://github.com/boostorg/hof/issues/128#issuecomment-195459323  

Well, it appears that Boost.Config's test depend on Boost.Core, which depends on Boost.Config. I really would like to use Boost.Config, but the cycles need to be removed before it can become a dependency of the Fit library.

---

## Comment 2

> Username: viboes  
> Created at: 2016-03-11 20:49:16 UTC  
> Url: https://github.com/boostorg/hof/issues/128#issuecomment-195545310  

And what do you propose if the cycle is not broken?

---

## Comment 3

> Username: pfultz2  
> Created at: 2016-03-11 20:58:29 UTC  
> Url: https://github.com/boostorg/hof/issues/128#issuecomment-195548222  

There is really only two remedies:  
- Continue using the current configuration macros  
- Build another configuration library for boost that does not have cycles  
  
I don't like either of these options.   
  
Another option could be to have Boost.Config detect whether Boost.Core is available, and then disable the tests that rely on that. However, I don't like that option either. I think the best thing would be for Boost.Config to fix its cycles. This is something that should be brought up on the boost mailing.

---

## Comment 4

> Username: pfultz2  
> Created at: 2016-03-21 23:41:17 UTC  
> Url: https://github.com/boostorg/hof/issues/128#issuecomment-199538662  

Actually, another option could be for Fit to use Boost.Config when available and then fallback on another mechanism when its not. This would allow for Fit to be installed modularly and take advantage of Boost.Config when installed as a monolithic boost library.

---

## Comment 5

> Username: pfultz2  
> Created at: 2016-04-28 03:43:12 UTC  
> Url: https://github.com/boostorg/hof/issues/128#issuecomment-215301760  

Ok, I am using the sd6 macros and falling back on C++ version to detect C++ features. I think this is more robust than Boost.Config. Of course, there is no sd6 macro for the workarounds(such as lack of expression sfinae), but every config macro is easily configurable by the user unlike Boost.Config.
