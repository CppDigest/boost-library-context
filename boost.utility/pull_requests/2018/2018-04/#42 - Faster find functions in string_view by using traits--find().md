# #42 Faster find functions in string_view by using traits::find() [Merged]

> Username: dimztimz  
> Created at: 2018-04-01 18:03:17 UTC  
> Updated at: 2018-04-18 14:57:39 UTC  
> Merged at: 2018-04-09 23:34:23 UTC  
> Closed at: 2018-04-09 23:34:23 UTC  
> Url: https://github.com/boostorg/utility/pull/42  

`char_traits::find()` gets optimized to `memchr()` which is really fast.

---

## Review 1 [Commented]

> Username: mclow  
> Created_at: 2018-04-01 18:55:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/42#pullrequestreview-108526287  

📁 include/boost/utility/string_view.hpp

```diff
 257 |+               return npos;
 258 |+             const charT* ret_ptr = traits::find(ptr_ + pos, len_ - pos, c);
 259 |+             if (ret_ptr)
```

> Username: mclow  
> Created_at: 2018-04-01 18:55:05 UTC  
> Updated_at: 2018-04-01 22:53:13 UTC  
> Url: https://github.com/boostorg/utility/pull/42#discussion_r178466262  

I think I'd write this as a trip-op: `return ret_ptr ? ret_ptr - ptr_ : npos;`


---

## Review 2 [Commented]

> Username: mclow  
> Created_at: 2018-04-01 18:58:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/42#pullrequestreview-108526340  

📁 include/boost/utility/string_view.hpp

```diff
 247 |               return pos;
 248 |-             const_iterator iter = std::search(this->cbegin() + pos, this->cend(),
 248 |+             const charT* first_char_ptr = traits::find(ptr_ + pos, len_ - pos, s[0]);
```

> Username: mclow  
> Created_at: 2018-04-01 18:58:18 UTC  
> Updated_at: 2018-04-01 22:53:13 UTC  
> Url: https://github.com/boostorg/utility/pull/42#discussion_r178466309  

I'm not 100% happy with this one.  The reason is that `std::search` will use `operator=`, and `traits::find` will use "whatever is supplied by the user" - and they may not match.   Also, this "find the first match, then compare the rest" is what most implementations of `search` do.  On the other hand, should we even be using `std::search` here - or should we use the traits? I need to research this.

> Username: mclow  
> Created_at: 2018-04-01 19:00:08 UTC  
> Updated_at: 2018-04-01 22:53:13 UTC  
> Url: https://github.com/boostorg/utility/pull/42#discussion_r178466340  

Maybe we could call `std::search` and pass `traits::eq` as the comparison predicate.

> Username: dimztimz  
> Created_at: 2018-04-01 19:43:31 UTC  
> Updated_at: 2018-04-02 11:11:43 UTC  
> Url: https://github.com/boostorg/utility/pull/42#discussion_r178467194  

> Maybe we could call std::search and pass traits::eq as the comparison predicate.  
  
This was already the case.  
  
I'd like to avoid std::search because the traits stuff should be faster. I once measured `string.find(char)` (which calls the `traits::find`) against `std::find` and the first is like 10x faster.  
  
One possible way is to find first matching character with `traits::find` and check the rest with `traits::compare`. if match, return, if no match repeat the search continuing immediately after `first_char_ptr`.  
This way we completely avoid std::search and rely only on traits. `traits::find` is optimized to `memchr`, and `traits::compare` is probably optimized to `memcmp`.


---

## Comment 3

> Username: dimztimz  
> Created_at: 2018-04-01 22:57:04 UTC  
> Url: https://github.com/boostorg/utility/pull/42#issuecomment-377823244  

Pushed another change.

---

## Comment 4

> Username: dimztimz  
> Created_at: 2018-04-02 11:50:53 UTC  
> Url: https://github.com/boostorg/utility/pull/42#issuecomment-377914095  

Some benchmarks http://quick-bench.com/AH7edVpaJbyHsgofNR_gSGTkWQs

---

## Comment 5

> Username: dimztimz  
> Created_at: 2018-04-09 11:33:20 UTC  
> Url: https://github.com/boostorg/utility/pull/42#issuecomment-379721479  

Another benchmark and small test suite. http://quick-bench.com/13XiveA3qdEut4BCdlypZoRWTwg  
Test the same code that is in this patch. Up to 14x speedup.  
  
Hopefully now this should be merged.

---

## Comment 6

> Username: mclow  
> Created_at: 2018-04-09 23:34:56 UTC  
> Url: https://github.com/boostorg/utility/pull/42#issuecomment-379925721  

Thanks for working on this. If you remind me in a week or so (after then tests have cycled), I will merge it to master.

---

## Comment 7

> Username: dimztimz  
> Created_at: 2018-04-18 14:57:39 UTC  
> Url: https://github.com/boostorg/utility/pull/42#issuecomment-382416843  

@mclow I am reminding you to merge into master.

---
