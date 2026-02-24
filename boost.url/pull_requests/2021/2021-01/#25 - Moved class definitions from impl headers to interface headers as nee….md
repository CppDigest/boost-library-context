# #25 Moved class definitions from impl headers to interface headers as nee… [Merged]

> Username: zajo  
> Created at: 2021-01-28 02:53:52 UTC  
> Updated at: 2021-01-29 01:26:27 UTC  
> Merged at: 2021-01-29 01:26:26 UTC  
> Closed at: 2021-01-29 01:26:26 UTC  
> Url: https://github.com/boostorg/url/pull/25  

…ded. Consistent application of inline throughout url_base/view sources.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2021-01-28 02:57:11 UTC  
> Url: https://github.com/boostorg/url/pull/25#issuecomment-768762401  

Commit message is too long, "Refactor source files" is better

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-28 02:57:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/25#pullrequestreview-577948778  

📁 include/boost/url/impl/url_base.hpp

```diff
  28 |+ iterator::
  29 |+ operator==(
  30 |+     iterator const& other) const noexcept
```

> Username: vinniefalco  
> Created_at: 2021-01-28 02:57:27 UTC  
> Updated_at: 2021-01-29 01:01:19 UTC  
> Url: https://github.com/boostorg/url/pull/25#discussion_r565786870  

iterators are passed by value

> Username: zajo  
> Created_at: 2021-01-28 03:11:59 UTC  
> Updated_at: 2021-01-29 01:01:19 UTC  
> Url: https://github.com/boostorg/url/pull/25#discussion_r565791288  

You've done this in many places, you want me to change it?

> Username: zajo  
> Created_at: 2021-01-28 03:15:39 UTC  
> Updated_at: 2021-01-29 01:01:19 UTC  
> Url: https://github.com/boostorg/url/pull/25#discussion_r565792201  

I changed it. You were only doing this in op== and op!=.

> Username: vinniefalco  
> Created_at: 2021-01-28 17:09:27 UTC  
> Updated_at: 2021-01-29 01:01:19 UTC  
> Url: https://github.com/boostorg/url/pull/25#discussion_r566261942  

If I passed by `const&`, it was wrong


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-28 17:06:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/25#pullrequestreview-578553156  

📁 include/boost/url/impl/url_view.hpp

```diff
  19 |+ iterator::
  20 |+ operator==(
  21 |+     iterator const& other) const noexcept
```

> Username: vinniefalco  
> Created_at: 2021-01-28 17:06:47 UTC  
> Updated_at: 2021-01-29 01:01:19 UTC  
> Url: https://github.com/boostorg/url/pull/25#discussion_r566259987  

iterators are passed by value


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-28 17:07:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/25#pullrequestreview-578553984  

📁 include/boost/url/url_base.hpp

```diff
1847 |+     using reference = value_type const&;
1848 |+ 
1849 |+     /// The difference_type for this iterator
```

> Username: vinniefalco  
> Created_at: 2021-01-28 17:07:41 UTC  
> Updated_at: 2021-01-29 01:01:19 UTC  
> Url: https://github.com/boostorg/url/pull/25#discussion_r566260661  

Use the brief from JSON, I think it is something about a signed integer type


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-28 17:07:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/25#pullrequestreview-578554102  

📁 include/boost/url/url_base.hpp

```diff
1838 |+         std::bidirectional_iterator_tag;
1839 |+ 
1840 |+     using value_type =
```

> Username: vinniefalco  
> Created_at: 2021-01-28 17:07:47 UTC  
> Updated_at: 2021-01-29 01:01:19 UTC  
> Url: https://github.com/boostorg/url/pull/25#discussion_r566260749  

These need javadocs


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-28 17:08:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/25#pullrequestreview-578554739  

📁 include/boost/url/url_base.hpp

```diff
1892 |+ 
1893 |+     iterator
1894 |+     operator--(int) noexcept
```

> Username: vinniefalco  
> Created_at: 2021-01-28 17:08:28 UTC  
> Updated_at: 2021-01-29 01:01:19 UTC  
> Url: https://github.com/boostorg/url/pull/25#discussion_r566261259  

These all need javadocs


---

## Comment 7

> Username: vinniefalco  
> Created_at: 2021-01-28 17:09:57 UTC  
> Url: https://github.com/boostorg/url/pull/25#issuecomment-769233384  

Since this is just shuffling code around, ignore my comments about javadocs for this pull request. However they will need to be added in a separate commit in another pull request.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2021-01-28 17:10:44 UTC  
> Url: https://github.com/boostorg/url/pull/25#issuecomment-769233910  

The commit message is too verbose:  
![image](https://user-images.githubusercontent.com/1503976/106173307-a1801f00-6148-11eb-8e40-914126e5ba27.png)  
  
Suggested: "Declarations are in public headers"

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2021-01-28 17:11:36 UTC  
> Url: https://github.com/boostorg/url/pull/25#issuecomment-769234715  

Never use past tense in commit messages, it is additional unnecessary verbosity

---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-29 01:23:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/25#pullrequestreview-578873637  

📁 include/boost/url/impl/url_base.hpp

```diff
  35 |+         n_ == other.n_);
  36 |+     return
  37 |+         v_ == other.v_ &&
```

> Username: vinniefalco  
> Created_at: 2021-01-29 01:23:56 UTC  
> Url: https://github.com/boostorg/url/pull/25#discussion_r566518326  

C++ standard leaves comparison of iterators from different containers as undefined. Thus this code is not quite correct. We should assert that the containers are the same. And when we return the value we should not check that the containers are the same.


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-29 01:24:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/25#pullrequestreview-578873783  

📁 include/boost/url/impl/url_base.hpp

```diff
  92 |+             nv_ == other.nv_));
  93 |+     return
  94 |+         v_ == other.v_ &&
```

> Username: vinniefalco  
> Created_at: 2021-01-29 01:24:20 UTC  
> Url: https://github.com/boostorg/url/pull/25#discussion_r566518445  

Same here about checking the container


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-29 01:24:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/25#pullrequestreview-578873884  

📁 include/boost/url/impl/url_base.ipp

```diff
1378 |-     v.a_.resize(v.size());
1378 |+     auto const s = v.a_.resize(v.size());
1379 |+     BOOST_ASSERT(v.s_ == s);
```

> Username: vinniefalco  
> Created_at: 2021-01-29 01:24:36 UTC  
> Url: https://github.com/boostorg/url/pull/25#discussion_r566518532  

This is unnecessary, you shouldn't be calling resize if the new size is smaller


---
