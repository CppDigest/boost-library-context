# #67 Default constructed buffers are not null [Closed]

> Username: vinniefalco  
> Created at: 2017-11-25 23:11:15 UTC  
> Updated at: 2017-12-02 07:29:54 UTC  
> Closed at: 2017-12-02 07:29:54 UTC  
> Url: https://github.com/boostorg/asio/pull/67  

This changes default construction of `const_buffer` and `mutable_buffer` to initialize the data pointer to a valid non-null address. This solves a problem where buffer_copy can end up calling memcpy with a null pointer, which results in undefined behavior.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-11-25 23:25:05 UTC  
> Url: https://github.com/boostorg/asio/pull/67#issuecomment-346972890  

An alternative is to simply check `n==0` in `detail::buffer_copy_1` before calling `memcpy`. I assume there's a reason that the check is not already there, so I proposed this pull request. Of course, if you're okay with just checking `n==0` that would be a much nicer fix.

---

## Review 2 [Commented]

> Username: DanielaE  
> Created_at: 2017-11-26 07:09:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/67#pullrequestreview-79018525  

📁 include/boost/asio/buffer.hpp

```diff
  97 |   {
  98 |+       static unsigned char c;
  99 |+       data_ = &c;
```

> Username: DanielaE  
> Created_at: 2017-11-26 07:08:03 UTC  
> Updated_at: 2017-11-26 07:09:29 UTC  
> Url: https://github.com/boostorg/asio/pull/67#discussion_r153061822  

I don't like the magic static in the constructor body for such trivial use cases. Wouldn't it be nicer to pull `static unsigned char c` out into namespace scope?

> Username: vinniefalco  
> Created_at: 2017-11-26 08:15:05 UTC  
> Updated_at: 2017-11-26 08:18:18 UTC  
> Url: https://github.com/boostorg/asio/pull/67#discussion_r153062979  

Technically, this shouldn't be a "magic static:"  
  
*The memory occupied by any object of static storage duration shall be zero-initialized  at  program startup before any other initialization takes place.*  
  
This can't be moved to namespace scope because it would make the library no longer header-only. I agree it is a little dirty, but I wanted to give Chris an alternative to just checking `n==0` before calling `memcmp` in case he had a reason for not doing the comparison.

---

📁 include/boost/asio/buffer.hpp

```diff
 230 |   const_buffer() BOOST_ASIO_NOEXCEPT
 230 |-     : data_(0),
 231 |+     : data_(""),
```

> Username: DanielaE  
> Created_at: 2017-11-26 07:09:12 UTC  
> Updated_at: 2017-11-26 07:09:29 UTC  
> Url: https://github.com/boostorg/asio/pull/67#discussion_r153061835  

This one is fine with me 😄


---

## Comment 3

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:29:54 UTC  
> Url: https://github.com/boostorg/asio/pull/67#issuecomment-348674738  

Issue is fixed in develop using approach from #68.

---
