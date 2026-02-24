# #55 - Increase limit 52 to 64..128 for boost/describe [Closed]

> Username: sigbjorn  
> Created at: 2025-08-08 07:18:01 UTC  
> Updated at: 2025-11-15 00:44:14 UTC  
> Closed at: 2025-11-15 00:44:14 UTC  
> Url: https://github.com/boostorg/describe/issues/55  

Thanks for excellent library that we use in Shyft to ease meta-programming related to introspection.  
  
We hit the 52 limit in pp_for_each.hpp on enum we use to enumerate messages in a tag-dispatch driven message protocol.  
  
The suggestion is to set it to 64, or maybe 128, that aligns with other macro limitations in boost.  
  
We have already created a local override for the boost/describe/detail/pp_for_each.hpp, so that there is currently no hurry, even if the changes are straight forward, just using existing macros and patterns.  
  
I will provide a PR for the affected file, so that maintainer work hopefully can be kept to a minimum.

---

## Comment 1

> Username: adamlopatowski-sys  
> Created at: 2025-11-14 20:34:25 UTC  
> Url: https://github.com/boostorg/describe/issues/55#issuecomment-3534472784  

@sigbjorn could you provide your workaround? I'm dealing with same problem.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-11-15 00:42:47 UTC  
> Url: https://github.com/boostorg/describe/issues/55#issuecomment-3535196742  

That was https://github.com/boostorg/describe/pull/56.

---

## Comment 3

> Username: pdimov  
> Created at: 2025-11-15 00:44:14 UTC  
> Url: https://github.com/boostorg/describe/issues/55#issuecomment-3535201265  

So I suppose this issue should have been closed when the PR was merged.
