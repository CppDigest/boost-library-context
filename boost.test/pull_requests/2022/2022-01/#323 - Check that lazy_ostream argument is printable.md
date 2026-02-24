# #323 Check that lazy_ostream argument is printable [Open]

> Username: ecatmur  
> Created at: 2022-01-18 14:05:40 UTC  
> Updated at: 2024-05-31 08:43:29 UTC  
> Url: https://github.com/boostorg/test/pull/323  

The operator() is not instantiated till later, which makes it  
difficult to locate an erroneous usage of <<.  Place a  
static_assert in the ctor (copied from print_helper.hpp) to check  
at construction time, which will point back to the << site.

---
