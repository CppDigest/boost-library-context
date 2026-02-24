# #329 [index_if] New index_if function [Merged]

> Username: ricejasonf  
> Created at: 2017-04-06 06:52:44 UTC  
> Updated at: 2017-04-07 23:20:07 UTC  
> Merged at: 2017-04-07 23:19:53 UTC  
> Closed at: 2017-04-07 23:19:53 UTC  
> Url: https://github.com/boostorg/hana/pull/329  

- Adds index_if  
    - Rewrites detail::index_if to use recursive alias stuff  
      optimized for tuple and basic_tuple  
    - find_if now uses index_if for Iterables  
    - at_key now uses index_if for Sequence  
    - Removes duplicate code and unnecessary special case implementations  
        - detail::advance_until  
        - at_key::advance_until  
        - tuple_tag implementation of find_if  
    - Uses Foldable instead of Sequence for cases where length is known.  
      (find_if had a specialization when Iterable and not Sequence)  
    - Adds test.*.auto.index_if for Sequences  
    - Adds test support/counter for testing infinite iterables  
  
I have yet to run any benchmarks on these changes.

---

## Review 1 [Changes requested]

> Username: ldionne  
> Created_at: 2017-04-06 16:15:15 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/hana/pull/329#pullrequestreview-31358386  

Looks good overall, minor changes requested.

📁 include/boost/hana/fwd/index_if.hpp

```diff
  22 |+     //! Given an `Iterable` structure `xs` and a predicate `pred`,
  23 |+     //! `index_if(xs, pred)` returns an `IntegralConstant` index *of* the first
  24 |+     //! element whose key satisfies the predicate. If no element satisfies the
```

> Username: ldionne  
> Created_at: 2017-04-06 16:06:03 UTC  
> Updated_at: 2017-04-07 04:17:53 UTC  
> Url: https://github.com/boostorg/hana/pull/329#discussion_r110202821  

Iterables have no notion of `key`, so just saying "index of the first element that satisfies the predicate" is enough.


📁 include/boost/hana/index_if.hpp

```diff
  14 |+ #include <boost/hana/fwd/index_if.hpp>
  15 |+ 
  16 |+ #include <boost/hana/fwd/at.hpp>
```

> Username: ldionne  
> Created_at: 2017-04-06 16:07:45 UTC  
> Updated_at: 2017-04-07 04:17:53 UTC  
> Url: https://github.com/boostorg/hana/pull/329#discussion_r110203215  

Nitpick, but please order the includes alphabetically.

---

📁 include/boost/hana/index_if.hpp

```diff
  99 |+         static constexpr auto apply(Xs const&, Pred const&)
 100 |+             -> typename detail::iterate_while<hana::size_c<0>,
 101 |+                 std::numeric_limits<std::size_t>::max(), false>
```

> Username: ldionne  
> Created_at: 2017-04-06 16:10:01 UTC  
> Updated_at: 2017-04-07 04:17:53 UTC  
> Url: https://github.com/boostorg/hana/pull/329#discussion_r110203705  

Since `size_t` is unsigned, and unsigned underflow is defined to wrap, I would suggest being bold and just using `static_cast<size_t>(-1)`. I normally wouldn't do this, but this will allow us to get rid of the `<limits>` include.


📁 test/_include/auto/index_if.hpp

```diff
  35 |+     ));
  36 |+     BOOST_HANA_CONSTANT_CHECK(hana::equal(
  37 |+         hana::index_if(MAKE_TUPLE(ct_eq<0>{}, ct_eq<1>{}, ct_eq<2>{}), hana::equal.to(ct_eq<1>{})),
```

> Username: ldionne  
> Created_at: 2017-04-06 16:11:40 UTC  
> Updated_at: 2017-04-07 04:17:53 UTC  
> Url: https://github.com/boostorg/hana/pull/329#discussion_r110204075  

Can you add a test case that goes like `index_if(MAKE_TUPLE(ct_eq<0>, ce_eq<1>, ...), equal.to(ct_eq<0>))`, please?

> Username: ricejasonf  
> Created_at: 2017-04-06 17:06:34 UTC  
> Updated_at: 2017-04-07 04:17:53 UTC  
> Url: https://github.com/boostorg/hana/pull/329#discussion_r110215983  

I also forgot an empty tuple.

---

📁 test/_include/auto/index_if.hpp

```diff
   7 |+ #define BOOST_HANA_TEST_AUTO_INDEX_IF_HPP
   8 |+ 
   9 |+ #include <boost/hana/assert.hpp>
```

> Username: ldionne  
> Created_at: 2017-04-06 16:12:12 UTC  
> Updated_at: 2017-04-07 04:17:53 UTC  
> Url: https://github.com/boostorg/hana/pull/329#discussion_r110204213  

Order includes please.


📁 test/index_if.cpp

```diff
  19 |+         return [=](auto x) {
  20 |+         return hana::bool_c<decltype(x)::value == decltype(i)::value>;
  21 |+         };};
```

> Username: ldionne  
> Created_at: 2017-04-06 16:13:12 UTC  
> Updated_at: 2017-04-07 04:17:53 UTC  
> Url: https://github.com/boostorg/hana/pull/329#discussion_r110204453  

Weird indentation.

---

📁 test/index_if.cpp

```diff
   1 |+ // Copyright Louis Dionne 2013-2017
```

> Username: ldionne  
> Created_at: 2017-04-06 16:14:27 UTC  
> Updated_at: 2017-04-07 04:17:53 UTC  
> Url: https://github.com/boostorg/hana/pull/329#discussion_r110204774  

Fuckin' A! I love tests that go through the trouble of testing infinite sequences, thanks for that.


---

## Comment 2

> Username: ricejasonf  
> Created_at: 2017-04-06 17:08:50 UTC  
> Url: https://github.com/boostorg/hana/pull/329#issuecomment-292241075  

@ldionne I made all changes that you requested and added an example.

---

## Review 3 [Commented]

> Username: ldionne  
> Created_at: 2017-04-07 01:10:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/329#pullrequestreview-31464536  

📁 include/boost/hana/index_if.hpp

```diff
  53 |+         struct iterate_while<i, N, false> {
  54 |+             template <typename F, typename Pred>
  55 |+             using f = typename iterate_while<i + 1, N,
```

> Username: ldionne  
> Created_at: 2017-04-07 01:10:55 UTC  
> Updated_at: 2017-04-07 04:17:53 UTC  
> Url: https://github.com/boostorg/hana/pull/329#discussion_r110299959  

We could be more compile-time efficient here if we hard-coded the `hana::at` call, since we would avoid instantiating `partial_at::operator()` on each iteration. Unless we reuse `iterate_while` somewhere else with a different `F`, I suggest we do that


---

## Review 4 [Commented]

> Username: ldionne  
> Created_at: 2017-04-07 01:13:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/329#pullrequestreview-31464816  

📁 include/boost/hana/fwd/index_if.hpp

```diff
  24 |+     //! first element that satisfies the predicate. If no element satisfies the
  25 |+     //! predicate one of two things may occur. For `Foldable` structures the
  26 |+     //! result will be an out-of-bounds `IntegralConstant` index, but for
```

> Username: ldionne  
> Created_at: 2017-04-07 01:13:49 UTC  
> Updated_at: 2017-04-07 04:17:53 UTC  
> Url: https://github.com/boostorg/hana/pull/329#discussion_r110300193  

The documentation here is incorrect. We return an optional index, not an index that is out of bounds. I'm not sure which one is better, actually. I think I'd go with the optional index, and fix the documentation, but I could easily be convinced otherwise.


---

## Comment 5

> Username: ricejasonf  
> Created_at: 2017-04-07 04:23:15 UTC  
> Updated_at: 2017-04-07 04:24:04 UTC  
> Url: https://github.com/boostorg/hana/pull/329#issuecomment-292431753  

@ldionne  
- I agree that optional is a better interface. I just forgot to change the docs.  
- I removed the unnecessary `partial_at` but then realized it was also being used in find_if.hpp,  
so I moved it to there. oops! :sweat_smile:   
  
Fixed those

---

## Comment 6

> Username: ldionne  
> Created_at: 2017-04-07 23:20:07 UTC  
> Url: https://github.com/boostorg/hana/pull/329#issuecomment-292674044  

Thank you!

---
