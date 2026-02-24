# #90 - For old gcc versions BOOST_MP11_HAS_TEMPLATE_AUTO isn't set [Open]

> Username: rolandschulz  
> Created at: 2024-01-07 09:20:19 UTC  
> Updated at: 2024-02-12 17:27:29 UTC  
> Url: https://github.com/boostorg/mp11/issues/90  

Old versions of gcc (7.1-8.2) don't define `__cpp_nontype_template_parameter_auto` but only `__cpp_template_auto`. Because of that `BOOST_MP11_HAS_TEMPLATE_AUTO`  doesn't get set even though those versions support it. spirit checks both (https://github.com/boostorg/spirit/blob/59515f0e56aebdf958eadab30be99cac8872e723/include/boost/spirit/home/x3.hpp#L19). Would this make sense for mp11 too?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-01-07 15:23:10 UTC  
> Url: https://github.com/boostorg/mp11/issues/90#issuecomment-1880088850  

Could be worth a try; looks like `__cpp_template_auto` is set for Clang down to 4.0, I wonder whether such an old compiler would be able to actually compile the things.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-01-07 16:24:33 UTC  
> Url: https://github.com/boostorg/mp11/issues/90#issuecomment-1880104276  

Looks like GCC 7 has a pre-standard implementation of `auto` in template parameters - it retains the value, but not the type, which leads to odd things like `0L` being treated as `false`. (Interestingly, this seems to be the case for MSVC 14.1 as well.)  
  
So this leaves 8.1 and 8.2, and I wonder whether it's worth the bother, as I see 8.4 on ubuntu-20.04.

---

## Comment 3

> Username: Lastique  
> Created at: 2024-02-12 17:27:28 UTC  
> Url: https://github.com/boostorg/mp11/issues/90#issuecomment-1939199027  

There is now `BOOST_NO_CXX17_AUTO_NONTYPE_TEMPLATE_PARAMS` in Boost.Config that indicates support for this feature. It includes older compilers.
