# #58 issue-56: fix binary serialization compatibility problem with time_duration [Merged]

> Username: jeking3  
> Created at: 2017-12-23 18:09:27 UTC  
> Updated at: 2017-12-30 22:16:31 UTC  
> Merged at: 2017-12-30 21:26:43 UTC  
> Closed at: 2017-12-30 21:26:43 UTC  
> Url: https://github.com/boostorg/date_time/pull/58  

This fixes #56

---

## Review 1 [Commented]

> Username: eldiener  
> Created_at: 2017-12-29 08:38:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/58#pullrequestreview-85925119  

📁 include/boost/date_time/time_resolution_traits.hpp

```diff
  69 | #endif
  70 |            unsigned short frac_digits,
  71 |-            typename var_type = std::time_t >
```

> Username: eldiener  
> Created_at: 2017-12-29 08:38:47 UTC  
> Updated_at: 2017-12-30 04:38:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/58#discussion_r159036564  

The type var_type defaults to a value, but that can be overridden. Does this affect the serialization code which you have corrected ? In other words can the end-user change the time_duration internal types by configuration time_resolution_traits differently ? If so, in that case isn't the correct code always to use the time_resolution_traits var_type as the type used to serialize rather than doing asserts to make sure the type is always the var_type default ?

> Username: jeking3  
> Created_at: 2017-12-29 14:24:32 UTC  
> Updated_at: 2017-12-30 04:38:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/58#discussion_r159066361  

So if we had the static assertions in the code before this, and they all used var_type, they would all have passed when var_type changed.  This would have defeated the purpose of the static asserts, which is to protect the binary format compatibility.  In Boost 1.65.1 and earlier var_type was `boost::int32_t` which had the year 2038 problem.  In 1.66.0 the var_type changed to `std::time_t`, thus causing it to vary on different platforms.  By changing it to boost::int64_t and ensuring the output size is 64-bits with static assertions, it protects the binary serialization.

> Username: mclow  
> Created_at: 2017-12-29 17:16:25 UTC  
> Updated_at: 2017-12-30 04:38:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/58#discussion_r159084150  

You should put that comment "_In Boost 1.65.1 and earlier var_type was boost::int32_t which had the year 2038 problem. In 1.66.0 the var_type changed to std::time_t, thus causing it to vary on different platforms. By changing it to boost::int64_t and ensuring the output size is 64-bits with static assertions, it protects the binary serialization._" in the source code, so that people will know why it is this way.

> Username: jeking3  
> Created_at: 2017-12-29 20:22:59 UTC  
> Updated_at: 2017-12-30 04:38:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/58#discussion_r159100580  

Okay, will do and push later.

> Username: eldiener  
> Created_at: 2017-12-29 20:46:56 UTC  
> Updated_at: 2017-12-30 04:38:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/58#discussion_r159102366  

Can the var_type be changed by the end-user ? Or is time_resolution_traits strictly used internally ? if the latter I have no objection to the serialization code in this fix. But if time_resolution_traits, and therefore var_type, can be used by the end-user to change the time duration types then I think the serialization code must use the var_type rather than assume a hardcoded setting which is merely the default type for the var_type.

> Username: jeking3  
> Created_at: 2017-12-30 04:32:31 UTC  
> Updated_at: 2017-12-30 04:38:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/58#discussion_r159116653  

I believe posix_time_config.hpp is the only consumer, and it relies on the default value.


---
