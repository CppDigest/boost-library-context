# #46 Adjust to changes in Boost.Parameter [Merged]

> Username: CromwellEnage  
> Created at: 2018-11-22 14:22:45 UTC  
> Updated at: 2019-01-06 05:54:04 UTC  
> Merged at: 2019-01-06 05:54:04 UTC  
> Closed at: 2019-01-06 05:54:04 UTC  
> Url: https://github.com/boostorg/lockfree/pull/46  

<boost/lockfree/policies.hpp>  
* Change include statement to reflect the fact that the boost::parameter::template_keyword class template was moved to <boost/parameter/template_keyword.hpp>.  
  
<boost/lockfree/detail/parameter.hpp>  
* Use boost::mpl::has_key vice boost::lockfree::detail::has_arg.

---

## Comment 1

> Username: timblechmann  
> Created_at: 2018-11-26 04:19:13 UTC  
> Url: https://github.com/boostorg/lockfree/pull/46#issuecomment-441514732  

thanks for the PR. could you possibly keep the style like in the rest of the code?

---

## Comment 2

> Username: CromwellEnage  
> Created_at: 2018-11-26 04:55:47 UTC  
> Url: https://github.com/boostorg/lockfree/pull/46#issuecomment-441519069  

Okay.

---
