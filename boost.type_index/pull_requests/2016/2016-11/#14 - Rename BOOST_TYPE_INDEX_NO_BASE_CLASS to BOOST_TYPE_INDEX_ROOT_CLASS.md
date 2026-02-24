# #14 Rename BOOST_TYPE_INDEX_NO_BASE_CLASS to BOOST_TYPE_INDEX_ROOT_CLASS. [Closed]

> Username: cdglove  
> Created at: 2016-11-05 21:19:41 UTC  
> Updated at: 2017-01-04 07:12:00 UTC  
> Closed at: 2017-01-04 07:12:00 UTC  
> Url: https://github.com/boostorg/type_index/pull/14  

This is slightly shorter and feels better without the negative in there.  
  
Despite this change, I'm still not happy with the current interface. I would prefer something like.  
  
BOOST_TYPE_INDEX_REGISTER_RUNTIME_CLASS(); // Set nothing when no bases.  
BOOST_TYPE_INDEX_REGISTER_RUNTIME_CLASS(base_1, base_1); // Simple comma separated list.  
  
This would be possible with a variadic macro implementation. Would there be any objections to a widening of the interface to contain a variadic version of  BOOST_TYPE_INDEX_REGISTER_RUNTIME_CLASS? Something like.  
BOOST_TYPE_INDEX_REGISTER_RUNTIME_CLASSES or BOOST_TYPE_INDEX_REGISTER_RUNTIME_CLASS_VARIADIC  
  
Note this pull request depends on PR #13, but I can separate them if 13 is not desired.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-01-03 20:10:32 UTC  
> Url: https://github.com/boostorg/type_index/pull/14#issuecomment-270211579  

I'm afraid it's too late. This would be a breaking change and for user's code

---

## Comment 2

> Username: cdglove  
> Created_at: 2017-01-03 20:15:23 UTC  
> Url: https://github.com/boostorg/type_index/pull/14#issuecomment-270212692  

Indeed. No worries, it's certainly not a big deal.

---
