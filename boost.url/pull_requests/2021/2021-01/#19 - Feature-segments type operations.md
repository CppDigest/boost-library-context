# #19 Feature/segments type operations [Closed]

> Username: zajo  
> Created at: 2021-01-10 00:45:54 UTC  
> Updated at: 2021-01-25 21:16:16 UTC  
> Closed at: 2021-01-25 21:16:16 UTC  
> Url: https://github.com/boostorg/url/pull/19  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-10 02:14:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/19#pullrequestreview-564823191  

📁 include/boost/url/impl/url_base.ipp

```diff
1338 | }
1339 | 
1340 |+ url_base::segments_type::iterator
```

> Username: vinniefalco  
> Created_at: 2021-01-10 02:14:56 UTC  
> Updated_at: 2021-01-12 01:15:45 UTC  
> Url: https://github.com/boostorg/url/pull/19#discussion_r554498466  

Use `auto` and trailing return type here so we don't have to utter a long type name.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-10 02:16:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/19#pullrequestreview-564823221  

📁 include/boost/url/impl/url_base.ipp

```diff
1351 |+ segments_type::
1352 |+ erase( url_base::segments_type::iterator b, url_base::segments_type::iterator e ) noexcept
1353 |+ {
```

> Username: vinniefalco  
> Created_at: 2021-01-10 02:16:08 UTC  
> Updated_at: 2021-01-12 01:15:45 UTC  
> Url: https://github.com/boostorg/url/pull/19#discussion_r554498576  

```  
auto  
url_base::  
segments_type::  
erase(  
    iterator first,   
    iterator last ) noexcept ->  
        iterator  
```


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-10 02:20:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/19#pullrequestreview-564823356  

📁 include/boost/url/impl/url_base.ipp

```diff
1367 |+     BOOST_ASSERT(v.pt_.nseg >= slcount);
1368 |+     v.pt_.nseg -= slcount;
1369 |+     std::copy(v.s_ + e.off_, v.s_ + v.pt_.offset[detail::id_end] + 1, v.s_ + b.off_);
```

> Username: vinniefalco  
> Created_at: 2021-01-10 02:20:05 UTC  
> Updated_at: 2021-01-12 01:15:45 UTC  
> Url: https://github.com/boostorg/url/pull/19#discussion_r554498892  

use `memcpy` instead of `copy`, to avoid including `<algorithm>`

> Username: vinniefalco  
> Created_at: 2021-01-10 02:26:30 UTC  
> Updated_at: 2021-01-12 01:15:45 UTC  
> Url: https://github.com/boostorg/url/pull/19#discussion_r554499382  

or umm... `memmove` :)


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-10 02:23:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/19#pullrequestreview-564823450  

📁 include/boost/url/impl/url_base.ipp

```diff
1377 |+ url_base::
1378 |+ segments_type::
1379 |+ insert_encoded_segment( segments_type::iterator at, string_view const enseg )
```

> Username: vinniefalco  
> Created_at: 2021-01-10 02:23:06 UTC  
> Updated_at: 2021-01-12 01:15:45 UTC  
> Url: https://github.com/boostorg/url/pull/19#discussion_r554499073  

Arguments passed by value never need to be marked `const`

> Username: zajo  
> Created_at: 2021-01-11 22:23:50 UTC  
> Updated_at: 2021-01-12 01:15:45 UTC  
> Url: https://github.com/boostorg/url/pull/19#discussion_r555378664  

They don't, and should never be, in declarations. In definitions, `const` indicates that the value will not change, which is useful information (I only put it in the definition when appropriate, never in the declaration).

> Username: vinniefalco  
> Created_at: 2021-01-12 01:24:18 UTC  
> Url: https://github.com/boostorg/url/pull/19#discussion_r555446743  

I dont use it


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-10 02:43:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/19#pullrequestreview-564824087  

📁 include/boost/url/url_base.hpp

```diff
1672 |+     BOOST_URL_DECL
1673 |+     iterator
1674 |+     insert_encoded_segment( iterator at, string_view encoded_segment );
```

> Username: vinniefalco  
> Created_at: 2021-01-10 02:43:08 UTC  
> Updated_at: 2021-01-12 01:15:45 UTC  
> Url: https://github.com/boostorg/url/pull/19#discussion_r554500624  

`encoded_segment ` should just be `s`

> Username: vinniefalco  
> Created_at: 2021-01-10 02:43:55 UTC  
> Updated_at: 2021-01-12 01:15:45 UTC  
> Url: https://github.com/boostorg/url/pull/19#discussion_r554500718  

`iterator pos` since that's what the standard uses for the formal argument name


---

## Comment 6

> Username: vinniefalco  
> Created_at: 2021-01-15 22:18:13 UTC  
> Url: https://github.com/boostorg/url/pull/19#issuecomment-761227409  

doc toolchain fixes are merged to develop

---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-25 00:50:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/19#pullrequestreview-575011420  

📁 include/boost/url/impl/url_base.hpp

```diff
  55 | 
  56 |+     using pointer =
  57 |+         value_type *;
```

> Username: vinniefalco  
> Created_at: 2021-01-25 00:50:53 UTC  
> Updated_at: 2021-01-25 00:50:54 UTC  
> Url: https://github.com/boostorg/url/pull/19#discussion_r563412788  

shouldn't this be `value_type const*`


---
