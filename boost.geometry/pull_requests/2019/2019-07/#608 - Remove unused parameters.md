# #608 Remove unused parameters [Merged]

> Username: vissarion  
> Created at: 2019-07-02 09:31:00 UTC  
> Updated at: 2019-07-03 08:34:15 UTC  
> Merged at: 2019-07-02 16:30:51 UTC  
> Closed at: 2019-07-02 16:30:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/608  

Remove unused parameters in some algorithms to avoid compilation warnings.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-02 11:06:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/608#pullrequestreview-256821008  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 163 |+ #if ! defined(BOOST_GEOMETRY_USE_RESCALING)
 164 |+             range_q
 165 |+ #endif
```

> Username: awulkiew  
> Created_at: 2019-07-02 11:06:53 UTC  
> Updated_at: 2019-07-02 16:29:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/608#discussion_r299427408  

I think I'd prefer passing them to `ignore_unused` to avoid ifdefs distorting reading of the code.

> Username: vissarion  
> Created_at: 2019-07-02 11:24:25 UTC  
> Updated_at: 2019-07-02 16:29:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/608#discussion_r299433055  

Thanks, I agree.

> Username: barendgehrels  
> Created_at: 2019-07-03 08:34:14 UTC  
> Updated_at: 2019-07-03 08:34:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/608#discussion_r299837100  

Thank you, I agree with that too.  
Furthermore I'm OK, thanks for the PR


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-02 16:23:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/608#pullrequestreview-257007313  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 162 |     {
 163 |+         boost::ignore_unused(range_p);
 164 |+         boost::ignore_unused(range_q);
```

> Username: awulkiew  
> Created_at: 2019-07-02 16:23:56 UTC  
> Updated_at: 2019-07-02 16:29:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/608#discussion_r299572041  

Btw, you could pass multiple arguments so this could be one line, but it's ok as it is.


---

## Comment 3

> Username: awulkiew  
> Created_at: 2019-07-02 18:13:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/608#issuecomment-507788443  

Btw, you could've amended the last commit and then force pushed instead of creating another one. ;)

---

## Comment 4

> Username: vissarion  
> Created_at: 2019-07-03 07:03:19 UTC  
> Updated_at: 2019-07-03 07:03:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/608#issuecomment-507967885  

I know, my intention is to use github's squash but it didn't worked as expected.

---
