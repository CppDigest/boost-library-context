# #35 Added boost.container.pmr to request. [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-10-30 14:53:24 UTC  
> Updated at: 2022-10-31 21:26:15 UTC  
> Closed at: 2022-10-31 21:26:15 UTC  
> Url: https://github.com/boostorg/redis/pull/35  



---

## Review 1 [Commented]

> Username: mzimbres  
> Created_at: 2022-10-30 15:23:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/35#pullrequestreview-1161152861  

📁 CMakeLists.txt

```diff
 103 | 
 101 |- target_link_libraries(intro_tls OpenSSL::Crypto OpenSSL::SSL)
 102 |- target_link_libraries(test_conn_tls OpenSSL::Crypto OpenSSL::SSL)
```

> Username: mzimbres  
> Created_at: 2022-10-30 15:23:32 UTC  
> Url: https://github.com/boostorg/redis/pull/35#discussion_r1008882630  

Why did you remove openssl dependency?

> Username: klemens-morgenstern  
> Created_at: 2022-10-31 03:14:13 UTC  
> Url: https://github.com/boostorg/redis/pull/35#discussion_r1008998016  

That would've been by accident.


---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2022-10-30 15:29:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/35#pullrequestreview-1161153746  

📁 include/aedis/resp3/request.hpp

```diff
 209 |-    : cfg_{cfg}
 210 |+    explicit request(boost::container::pmr::memory_resource * resource)
 211 |+          : payload_(resource), cfg_{false, true, false, true}
```

> Username: mzimbres  
> Created_at: 2022-10-30 15:29:18 UTC  
> Updated_at: 2022-10-30 15:33:39 UTC  
> Url: https://github.com/boostorg/redis/pull/35#discussion_r1008883519  

AFAICS, we don't need this overload, the one below is enough. I wouldn't like to have `config{false, true, false, true}` twice in the code.


---

## Comment 3

> Username: mzimbres  
> Created_at: 2022-10-30 15:34:52 UTC  
> Url: https://github.com/boostorg/redis/pull/35#issuecomment-1296286086  

` boost::container::pmr::string` was indeed a good idea.

---

## Comment 4

> Username: mzimbres  
> Created_at: 2022-10-30 15:54:25 UTC  
> Url: https://github.com/boostorg/redis/pull/35#issuecomment-1296289601  

Does the serialization example still work? See https://github.com/mzimbres/aedis/blob/ec6e99d99a9cbc62b06a7362904bd343b37ca008/examples/serialization.cpp#L60

---

## Comment 5

> Username: mzimbres  
> Created_at: 2022-10-30 18:55:09 UTC  
> Url: https://github.com/boostorg/redis/pull/35#issuecomment-1296326809  

I see two downsides about using `boost::container::pmr::string` instead of `std::string` with template allocator in the request  
* All request member functions are template anyway, which means I won't be able to move some code to `cpp` files.  
* boost::container is a new dependency that must be built  and linked against. CMake can't find it in my installation and I wouldn't like to force this on users.  
  
I will sketch a solution with std::string based on this PR, let me see how it compares.

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2022-10-31 03:16:36 UTC  
> Url: https://github.com/boostorg/redis/pull/35#issuecomment-1296483519  

If you're planning to stay on C++17 you can just use std::pmr instead: https://en.cppreference.com/w/cpp/header/memory_resource

---

## Comment 7

> Username: mzimbres  
> Created_at: 2022-10-31 21:26:04 UTC  
> Url: https://github.com/boostorg/redis/pull/35#issuecomment-1297705354  

Merged this branch locally after moving to std::pmr::string and other unrelated changes: https://github.com/mzimbres/aedis/commit/f93f3cab58ddec2b90ec353a6b65892135ca94d2

---
