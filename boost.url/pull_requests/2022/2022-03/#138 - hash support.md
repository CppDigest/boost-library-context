# #138 hash support [Merged]

> Username: alandefreitas  
> Created at: 2022-03-14 19:27:13 UTC  
> Updated at: 2022-03-16 15:32:26 UTC  
> Merged at: 2022-03-16 01:06:51 UTC  
> Closed at: 2022-03-16 01:06:51 UTC  
> Url: https://github.com/boostorg/url/pull/138  

fix #64

---

## Review 1 [Commented]

> Username: alandefreitas  
> Created_at: 2022-03-14 19:28:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-909293736  

📁 include/boost/url/detail/impl/normalize.ipp

```diff
  22 | 
  23 |+ void
  24 |+ pop_encoded_front(
```

> Username: alandefreitas  
> Created_at: 2022-03-14 19:28:19 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826305778  

This was just extracted from `compare_encoded` so it could be reused. Same code here.


---

## Review 2 [Commented]

> Username: alandefreitas  
> Created_at: 2022-03-14 19:30:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-909295681  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 208 |+ 
 209 |+ void
 210 |+ pop_last_segment(
```

> Username: alandefreitas  
> Created_at: 2022-03-14 19:30:15 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826307144  

This was just extracted from `normalized_path_compare` so it can be reused in the hash function. Same code.


---

## Review 3 [Commented]

> Username: alandefreitas  
> Created_at: 2022-03-14 19:31:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-909296720  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 575 |+         {
 576 |+             char c = path_pop_back(child);
 577 |+             hash = (c ^ hash) * prime;
```

> Username: alandefreitas  
> Created_at: 2022-03-14 19:31:15 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826307879  

This logic came from boost.json string. I don't know if there's anything we have to adjust here.

> Username: vinniefalco  
> Created_at: 2022-03-14 20:56:55 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826372248  

hmmm `c ^ hash` is not producing much information. Ideally `c` would be the same number of bits as `hash`.

> Username: vinniefalco  
> Created_at: 2022-03-14 22:41:43 UTC  
> Updated_at: 2022-03-14 22:41:44 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826437932  

it turns out boost.json is wrong, see https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function


---

## Review 4 [Commented]

> Username: alandefreitas  
> Created_at: 2022-03-14 19:32:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-909297823  

📁 include/boost/url/impl/url_view.ipp

```diff
 459 |-         scheme(),
 460 |-         other.scheme());
 459 |+         get(id_scheme),
```

> Username: alandefreitas  
> Created_at: 2022-03-14 19:32:21 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826308723  

I missed last time we need the complete thing (`get(...)`) to ensure we eliminate some false positives.

> Username: vinniefalco  
> Created_at: 2022-03-14 22:33:11 UTC  
> Updated_at: 2022-03-14 22:33:12 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826433551  

did you add a test? Is this for hash or is this for compare?

> Username: alandefreitas  
> Created_at: 2022-03-14 23:18:59 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826455319  

It's for compare. The previous tests are still valid. It's just a matter of whether the surrounding reserved chars are considered in the comparison. There weren't considered but I realized they need to be considered.


---

## Review 5 [Commented]

> Username: alandefreitas  
> Created_at: 2022-03-14 19:36:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-909302842  

📁 include/boost/url/impl/url_view.ipp

```diff
 834 |+ {
 835 |+ #if BOOST_URL_ARCH == 64
 836 |+     std::uint64_t const prime = 0x100000001B3ULL;
```

> Username: alandefreitas  
> Created_at: 2022-03-14 19:36:57 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826312330  

These constants came from boost.json, but I don't know the procedure for coming up with new constants.

> Username: vinniefalco  
> Created_at: 2022-03-14 20:56:25 UTC  
> Updated_at: 2022-03-14 20:56:26 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826371700  

You don't need new constants, those are fine (I think). It would have to be analyzed mathematically. Out of scope for now, until someone complains.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-14 19:37:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-909303481  

📁 include/boost/url/detail/config.hpp

```diff
  81 |+ #elif UINTPTR_MAX == UINT32_MAX
  82 |+ # define BOOST_URL_ARCH 32
  83 |+ #else
```

> Username: vinniefalco  
> Created_at: 2022-03-14 19:37:34 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826312780  

nice


---

## Review 7 [Commented]

> Username: alandefreitas  
> Created_at: 2022-03-14 19:38:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-909304598  

📁 include/boost/url/impl/url_view.ipp

```diff
 852 |+     ::boost::urls::detail::digest(
 853 |+         u.port(), hash, prime);
 854 |+     ::boost::urls::detail::normalized_path_digest(
```

> Username: alandefreitas  
> Created_at: 2022-03-14 19:38:44 UTC  
> Updated_at: 2022-03-14 23:16:12 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826313595  

One thing here is the path is being digested from right to left. I don't know if this can cause any problem. The symmetry with `operator==` still holds.  
  
I don't think there's a way to do that from left to right for the same reasons we discussed for `url_view::compare`

> Username: vinniefalco  
> Created_at: 2022-03-14 20:55:33 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826370765  

dude we do not "digest children"


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-14 19:40:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-909306330  

📁 include/boost/url/detail/impl/remove_dot_segments.ipp

```diff
 272 |+         std::size_t p = s.size() > 1
 273 |+                             ? s.find_last_of('/', s.size() - 2)
 274 |+                             : string_view::npos;
```

> Username: vinniefalco  
> Created_at: 2022-03-14 19:40:29 UTC  
> Updated_at: 2022-03-14 19:40:30 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r826314831  

weird format


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-15 16:05:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-910461708  

📁 include/boost/url/detail/normalize.hpp

```diff
  33 |+ 
  34 |+     explicit
  35 |+     fnv_1a(std::size_t salt)
```

> Username: vinniefalco  
> Created_at: 2022-03-15 16:05:52 UTC  
> Updated_at: 2022-03-15 16:05:53 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827153971  

noexcept?

> Username: vinniefalco  
> Created_at: 2022-03-15 16:06:22 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827154541  

hmm.... the salt...


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-15 16:06:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-910463099  

📁 include/boost/url/detail/normalize.hpp

```diff
  42 |+     put(char c)
  43 |+     {
  44 |+         h_ = (c ^ h_) * prime;
```

> Username: vinniefalco  
> Created_at: 2022-03-15 16:06:44 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827154915  

will `c` be promoted to `size_t` before performing xor?

> Username: alandefreitas  
> Created_at: 2022-03-15 16:20:02 UTC  
> Updated_at: 2022-03-15 16:20:03 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827169434  

I experimented in the compiler explorer and that's what happened. Is there any problem here? Should I have an explicit static_cast?

> Username: vinniefalco  
> Created_at: 2022-03-15 18:42:02 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827303623  

I would write it as `h_ ^ c` to match the algorithm specification


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-15 16:07:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-910463522  

📁 include/boost/url/detail/normalize.hpp

```diff
  55 |+     }
  56 |+ 
  57 |+     inline
```

> Username: vinniefalco  
> Created_at: 2022-03-15 16:07:00 UTC  
> Updated_at: 2022-03-15 16:07:01 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827155208  

don't need inline here or above

> Username: alandefreitas  
> Created_at: 2022-03-15 16:20:26 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827169819  

Oh... true


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-15 16:08:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-910465464  

📁 include/boost/url/detail/remove_dot_segments.hpp

```diff
  47 |+     bool remove_unmatched,
  48 |+     std::size_t& hash,
  49 |+     std::size_t prime) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-03-15 16:08:15 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827156506  

what is this `prime`?

> Username: alandefreitas  
> Created_at: 2022-03-15 16:26:46 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827176749  

Oh... I replaced it with `fnv_1a& hasher` in `remove_dot_segments.ipp` but forgot to do it in `remove_dot_segments.hpp` for some reason.   
  
Because this is only being called from `url_view.ipp`, it worked anyway. `url_view.ipp` found the not purposeful declaration and definition in `remove_dot_segments.ipp` while the declaration in `remove_dot_segments.hpp` just got ignored.


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-15 23:11:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-910927152  

📁 include/boost/url/url_view.hpp

```diff
1845 |+ 
1846 |+         @par Complexity
1847 |+         Constant or linear in `lhs.size()`.
```

> Username: vinniefalco  
> Created_at: 2022-03-15 23:11:41 UTC  
> Updated_at: 2022-03-15 23:11:42 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827489025  

when will complexity ever be constant?


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-15 23:12:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-910927341  

📁 include/boost/url/url_view.hpp

```diff
1848 |+ 
1849 |+         @par Exception Safety
1850 |+         No-throw guarantee.
```

> Username: vinniefalco  
> Created_at: 2022-03-15 23:12:01 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827489187  

Throws nothing.


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-15 23:12:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/138#pullrequestreview-910927717  

📁 include/boost/url/detail/normalize.hpp

```diff
  33 |+ 
  34 |+     explicit
  35 |+     fnv_1a(std::size_t salt) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-03-15 23:12:45 UTC  
> Url: https://github.com/boostorg/url/pull/138#discussion_r827489495  

are there any tests which use salted hash functions?


---
