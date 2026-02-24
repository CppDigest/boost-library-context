# #45 Add std::hash and boost::hash support [Merged]

> Username: jeking3  
> Created at: 2019-06-06 13:53:42 UTC  
> Updated at: 2019-06-07 17:56:05 UTC  
> Merged at: 2019-06-07 17:55:58 UTC  
> Closed at: 2019-06-07 17:55:59 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45  

This fixes #34

---

## Review 1 [Commented]

> Username: glenfe  
> Created_at: 2019-06-07 11:36:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#pullrequestreview-247046056  

📁 test/test_boost_hash.cpp

```diff
  14 |+ #include <boost/config.hpp>
  15 |+ #include <boost/dynamic_bitset.hpp>
  16 |+ #include <boost/detail/lightweight_test.hpp>
```

> Username: glenfe  
> Created_at: 2019-06-07 11:36:08 UTC  
> Updated_at: 2019-06-07 15:06:47 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#discussion_r291555124  

This is the deprecated header. Should include <boost/core/lightweight_test.hpp>

> Username: jeking3  
> Created_at: 2019-06-07 14:25:23 UTC  
> Updated_at: 2019-06-07 15:06:47 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#discussion_r291615562  

Thanks, fixed in the next push.


---

## Review 2 [Changes requested]

> Username: glenfe  
> Created_at: 2019-06-07 11:45:30 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#pullrequestreview-247049185  

📁 include/boost/dynamic_bitset/dynamic_bitset.hpp

```diff
1632 |+ {
1633 |+     std::size_t res = hash_value(a.m_num_bits);
1634 |+     hash_combine(res, hash_value(a.m_bits));
```

> Username: glenfe  
> Created_at: 2019-06-07 11:45:16 UTC  
> Updated_at: 2019-06-07 15:06:47 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#discussion_r291557509  

This does not look right to me. Why are you calling hash_value in the call to hash_combine?

> Username: jeking3  
> Created_at: 2019-06-07 14:25:30 UTC  
> Updated_at: 2019-06-07 15:06:47 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#discussion_r291615601  

Thanks, fixed in the next push.


---

## Review 3 [Commented]

> Username: glenfe  
> Created_at: 2019-06-07 12:12:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#pullrequestreview-247059520  

📁 include/boost/dynamic_bitset/dynamic_bitset.hpp

```diff
2123 | 
2124 |+ // std::hash support
2125 |+ #if (!defined(BOOST_NO_CXX11_HDR_FUNCTIONAL) || !defined(BOOST_NO_CXX11_NOEXCEPT)) && !defined(BOOST_DYNAMIC_BITSET_NO_STD_HASH)
```

> Username: glenfe  
> Created_at: 2019-06-07 12:12:29 UTC  
> Updated_at: 2019-06-07 15:06:47 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#discussion_r291565373  

The "or" in this check is questionable. In any case the check for `BOOST_NO_CXX11_NOEXCEPT` doesn't seem worth it here but instead just use `BOOST_NOEXCEPT` instead of `noexcept` below.

> Username: jeking3  
> Created_at: 2019-06-07 14:25:35 UTC  
> Updated_at: 2019-06-07 15:06:47 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#discussion_r291615636  

Thanks, fixed in the next push.


---

## Review 4 [Commented]

> Username: glenfe  
> Created_at: 2019-06-07 15:00:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#pullrequestreview-247146831  

📁 include/boost/dynamic_bitset/dynamic_bitset.hpp

```diff
1632 |+ {
1633 |+     std::size_t res = hash_value(a.m_num_bits);
1634 |+     hash_combine(res, a.m_bits);
```

> Username: glenfe  
> Created_at: 2019-06-07 15:00:31 UTC  
> Updated_at: 2019-06-07 15:06:47 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#discussion_r291631545  

`hash_combine` is not an ADL customization point like `hash_value` is. Qualify as `boost::hash_combine` here.

> Username: jeking3  
> Created_at: 2019-06-07 15:08:08 UTC  
> Updated_at: 2019-06-07 15:08:09 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#discussion_r291634688  

Thanks - pushed a fix.


---

## Comment 5

> Username: jeking3  
> Created_at: 2019-06-07 15:29:21 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/45#issuecomment-499930648  

Thanks for the second pair of eyes, definitely caught a major issue.

---
