# #800 [overlay] move segment_count_on_ring and segment_distance to get_ring, and add unit tests [Merged]

> Username: barendgehrels  
> Created at: 2021-01-27 13:12:06 UTC  
> Updated at: 2021-06-02 09:47:43 UTC  
> Merged at: 2021-02-19 09:38:58 UTC  
> Closed at: 2021-02-19 09:38:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/800  

This is a preparation step for next PR, which will be larger.  
Because I need `segment_distance` for an update in turn clusters too.  
  
This smaller PR is just a move, just refactoring.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-01-27 13:13:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/800#pullrequestreview-577320119  

📁 test/count_set.hpp

```diff
  54 |        os << "{";
  57 |-        BOOST_FOREACH(std::size_t const& value, s.m_values)
  55 |+        for(std::size_t const& value : s.m_values)
```

> Username: barendgehrels  
> Created_at: 2021-01-27 13:13:27 UTC  
> Updated_at: 2021-02-19 09:37:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/800#discussion_r565296707  

This is non related but I just got it in too

> Username: vissarion  
> Created_at: 2021-02-12 11:06:19 UTC  
> Updated_at: 2021-02-19 09:37:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/800#discussion_r575146883  

great! But there is a space missing after `for`

> Username: barendgehrels  
> Created_at: 2021-02-19 09:38:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/800#discussion_r579050846  

:heavy_check_mark: thanks, fixed


---

## Comment 2

> Username: barendgehrels  
> Created_at: 2021-02-10 09:30:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/800#issuecomment-776571906  

Are you OK with this PR?

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2021-02-12 11:08:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/800#pullrequestreview-589364542  

thanks! OK for merging!

📁 test/count_set.hpp

```diff
  84 |             // One of them is optional, add the second
  87 |-             BOOST_FOREACH(std::size_t const& value, a)
  85 |+             for(std::size_t const& value : a)
```

> Username: vissarion  
> Created_at: 2021-02-12 11:08:09 UTC  
> Updated_at: 2021-02-19 09:37:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/800#discussion_r575147930  

here too

> Username: barendgehrels  
> Created_at: 2021-02-19 09:38:38 UTC  
> Updated_at: 2021-02-19 09:38:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/800#discussion_r579051119  

:heavy_check_mark:


---
