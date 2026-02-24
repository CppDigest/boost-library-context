# #24 Fix for Ticket #11860 [Merged]

> Username: scopeInfinity  
> Created at: 2015-12-28 07:31:59 UTC  
> Updated at: 2016-08-29 06:10:55 UTC  
> Merged at: 2016-08-29 06:10:54 UTC  
> Closed at: 2016-08-29 06:10:54 UTC  
> Url: https://github.com/boostorg/iostreams/pull/24  

Undo use of  
_boost/type_traits/detail/bool_trait_def.hpp_ and _boost/type_traits/detail/template_arity_spec.hpp_,  
 Since, use of these header is deprecated.

---

## Comment 1

> Username: iljah  
> Created_at: 2015-12-28 10:43:31 UTC  
> Url: https://github.com/boostorg/iostreams/pull/24#issuecomment-167540798  

Does this fix similar warnings in other places besides iostreams? Thanks!

---

## Comment 2

> Username: scopeInfinity  
> Created_at: 2015-12-28 11:20:48 UTC  
> Url: https://github.com/boostorg/iostreams/pull/24#issuecomment-167545383  

I did a grep search, for _"is deprecated\")"_.  
I was able to find _boost/type_traits/detail/bool_trait_def.hpp_ and _boost/type_traits/detail/template_arity_spec.hpp_ out of grep search which are included anywhere. I couldn't find them included besides _iostreams_.  
Thank you

---

## Comment 3

> Username: iljah  
> Created_at: 2016-01-05 05:44:25 UTC  
> Url: https://github.com/boostorg/iostreams/pull/24#issuecomment-168904981  

template_arity_spec.hpp is included at least in:  
  
```  
grep -nr template_arity_spec.hpp .  
./function_types/result_type.hpp:16:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/components.hpp:18:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/is_function_pointer.hpp:13:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/member_function_pointer.hpp:13:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/function_reference.hpp:13:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/function_arity.hpp:16:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/parameter_types.hpp:16:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/member_object_pointer.hpp:13:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/is_member_object_pointer.hpp:13:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/is_function.hpp:12:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/is_callable_builtin.hpp:13:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/is_member_pointer.hpp:12:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/function_pointer.hpp:13:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/is_nonmember_callable_builtin.hpp:13:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/is_member_function_pointer.hpp:13:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./function_types/is_function_reference.hpp:13:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./iostreams/detail/is_dereferenceable.hpp:13:# include <boost/type_traits/detail/template_arity_spec.hpp>  
./type_traits/detail/bool_trait_def.hpp:21:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./type_traits/detail/size_t_trait_def.hpp:14:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./type_traits/detail/type_trait_def.hpp:14:#include <boost/type_traits/detail/template_arity_spec.hpp>  
./detail/is_incrementable.hpp:7:# include <boost/type_traits/detail/template_arity_spec.hpp>  
```

---

## Comment 4

> Username: iljah  
> Created_at: 2016-01-05 06:02:50 UTC  
> Url: https://github.com/boostorg/iostreams/pull/24#issuecomment-168908091  

Looks like the function_types and type_traits includes don't exist in git maybe they'll end up in 1.61.0...

---

## Comment 5

> Username: scopeInfinity  
> Created_at: 2016-01-05 07:23:17 UTC  
> Url: https://github.com/boostorg/iostreams/pull/24#issuecomment-168922200  

In other words, this PR is only for fix in _iostreams_

---

## Comment 6

> Username: MarcelRaad  
> Created_at: 2016-01-05 09:50:10 UTC  
> Url: https://github.com/boostorg/iostreams/pull/24#issuecomment-168952153  

This seems to be a duplicate of #15 ?

---

## Comment 7

> Username: zyv  
> Created_at: 2016-03-08 09:28:32 UTC  
> Url: https://github.com/boostorg/iostreams/pull/24#issuecomment-193686769  

This PR misses the (implicitly included elsewhere, but good for IWYU)  
  
```  
# include <boost/type_traits/integral_constant.hpp>  
```  
  
and it's unclear whether `BOOST_MPL_AUX_LAMBDA_SUPPORT` is still needed, but, on the other hand, #15 misses the `::` in front of `boost::integral_constant`.  
  
Nothing in this world is perfect :-)

---

## Comment 8

> Username: saturnjct  
> Created_at: 2016-03-28 19:37:38 UTC  
> Url: https://github.com/boostorg/iostreams/pull/24#issuecomment-202549536  

Any chance that this change can make it into Boost 1.61.0?

---

## Comment 9

> Username: ulidtko  
> Created_at: 2016-06-17 16:55:48 UTC  
> Url: https://github.com/boostorg/iostreams/pull/24#issuecomment-226822953  

Thanks for sharing the patch!  
  
I'm including it in our local Boost build for the team. Works just fine. So satisfying to see clean build logs again!  
  
Hope this PR gets merged into 1.61.0 (or at all; the sooner the better). Cheers!

---

## Comment 10

> Username: Bklyn  
> Created_at: 2016-06-17 20:11:50 UTC  
> Url: https://github.com/boostorg/iostreams/pull/24#issuecomment-226869992  

Hoping for 1.62...

---
