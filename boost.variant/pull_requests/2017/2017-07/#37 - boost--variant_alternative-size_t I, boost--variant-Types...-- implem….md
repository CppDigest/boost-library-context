# #37 boost::variant_alternative<size_t I, boost::variant<Types...>> implem… [Open]

> Username: very-cool-name  
> Created at: 2017-07-16 14:32:12 UTC  
> Updated at: 2017-08-10 11:55:20 UTC  
> Url: https://github.com/boostorg/variant/pull/37  

…entation. Uses variadic templates for modern compilers and boost::mpl magic for older ones. Tested on gcc 4.9.3, 6.3.0 and msvc 14.1. I'll add documentation little bit later.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-08-10 05:21:18 UTC  
> Url: https://github.com/boostorg/variant/pull/37#issuecomment-321453613  

I'm afraid this won't work for variants that were created using `make_variant_over`. I'm also not sure that this will work for variants that hold a reference type.  
  
Please add more tests.

---

## Comment 2

> Username: very-cool-name  
> Created_at: 2017-08-10 11:55:20 UTC  
> Url: https://github.com/boostorg/variant/pull/37#issuecomment-321530546  

There is test for `make_variant_over` it is called `test_over_sequence_type`. Is it insufficient? I'll add tests for reference types.

---
