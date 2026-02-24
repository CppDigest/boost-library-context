# #52 make string_view partially model Sequence concept [Closed]

> Username: huangqinjin  
> Created at: 2018-10-19 09:41:10 UTC  
> Updated at: 2019-01-08 03:53:56 UTC  
> Closed at: 2019-01-08 03:53:56 UTC  
> Url: https://github.com/boostorg/utility/pull/52  

With this commit, we can use boost::string_view with Boost.StringAlgo, such as `trim_copy` and `split`.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-10-19 14:05:26 UTC  
> Url: https://github.com/boostorg/utility/pull/52#issuecomment-431374594  

Needs tests. Both tests that the code works, and that it fulfills the Range Concept. Also, it would be nice if there were StringAlgo tests that use `string_view`

---

## Comment 2

> Username: huangqinjin  
> Created_at: 2018-10-20 05:00:39 UTC  
> Url: https://github.com/boostorg/utility/pull/52#issuecomment-431549066  

`string_view` already models Range concept, but some string algorithms require Sequence concept.

---

## Review 3 [Commented]

> Username: mclow  
> Created_at: 2018-10-20 15:01:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/52#pullrequestreview-166752103  

📁 test/string_view_test1.cpp

```diff
  32 |-     string_view sr2 ( NULL, 0 );
  33 |-     string_view sr3 ( p, 0 );
  32 |+     string_view sr2 ( NULL, std::size_t(0) );
```

> Username: mclow  
> Created_at: 2018-10-20 15:01:42 UTC  
> Url: https://github.com/boostorg/utility/pull/52#discussion_r226829882  

Requiring cast to `size_t` to remove ambiguity will break existing code, not just break the tests.

> Username: huangqinjin  
> Created_at: 2018-10-22 02:48:53 UTC  
> Updated_at: 2018-10-22 02:48:56 UTC  
> Url: https://github.com/boostorg/utility/pull/52#discussion_r226894642  

I think explicitly passing literal 0 to the second parameter is rarely used.


---
