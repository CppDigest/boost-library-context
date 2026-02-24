# #157 bidirectional segment iterators [Merged]

> Username: alandefreitas  
> Created at: 2022-03-28 23:40:52 UTC  
> Updated at: 2022-03-29 23:21:47 UTC  
> Merged at: 2022-03-29 23:21:47 UTC  
> Closed at: 2022-03-29 23:21:47 UTC  
> Url: https://github.com/boostorg/url/pull/157  

fix #150  
  
Work in progress

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 01:15:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-923915788  

📁 include/boost/url/impl/segments_encoded.hpp

```diff
 290 |             first, last));
 408 |-     return { *u_, before.i_ };
 291 |+     return std::next(begin(), before.i_);
```

> Username: vinniefalco  
> Created_at: 2022-03-29 01:15:20 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r836971820  

umm.... you are incrementing `before.i_` times?

> Username: alandefreitas  
> Created_at: 2022-03-29 01:19:14 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r836974470  

Yep. Because this is not constant, the pointers get invalidated when we change the URL. I think I sent a comment about that earlier today. I can only see two solutions here:  
  
- Recreate and increment `before.i_` times. It's at least not a bad placeholder because the complexity is still linear.  
- Reimplement all iterators to make them use offsets instead of pointers.

> Username: vinniefalco  
> Created_at: 2022-03-29 01:48:22 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r836985228  

We can always improve it later I guess, without changing the API


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 01:20:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-923917108  

📁 include/boost/url/impl/segments_encoded.ipp

```diff
  81 |+     // "/" segment
  82 |+     pct_encoded_str t;
  83 |+     bnf_t::increment(
```

> Username: vinniefalco  
> Created_at: 2022-03-29 01:18:33 UTC  
> Updated_at: 2022-03-29 01:20:15 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r836974230  

We can do better than this though. Since the string was already validated, we can skip the checking of percent-encoded characters and just do a find for the next slash.

> Username: alandefreitas  
> Created_at: 2022-03-29 01:21:37 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r836975274  

Yep. I thought about that but didn't want to mess with your logic.

> Username: alandefreitas  
> Created_at: 2022-03-29 18:22:11 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837779608  

I experimented with `find_first_of('/')`. The problem was `find_first_of('/')` plus the edge cases (`next_ == end_`, etc...) became very similar to just replicating the logic from `bnf_t::increment`.


📁 include/boost/url/segments_encoded.hpp

```diff
  83 |-         proxy which allows conversion to stringlike
  84 |-         types, assignments which change the underlying
  85 |-         container, and comparisons.
```

> Username: vinniefalco  
> Created_at: 2022-03-29 01:19:21 UTC  
> Updated_at: 2022-03-29 01:20:15 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r836974528  

oh yeah this doc is way out of date...we experimenting with an assignment-capable proxy and the results were terrible.

> Username: alandefreitas  
> Created_at: 2022-03-29 01:22:21 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r836975559  

Mmm... I didn't know about that.   
  
I'll remove that. I thought the intention was to still implement something like that in the future.

> Username: vinniefalco  
> Created_at: 2022-03-29 02:22:39 UTC  
> Updated_at: 2022-03-29 02:22:40 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r836997451  

hell no it was painful to implement and awkward to use, due to limitations in the language


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 02:23:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-923947206  

📁 include/boost/url/impl/segments_encoded.ipp

```diff
 120 |+         pct_encoded_str t;
 121 |+         bnf_t::increment(next_,
 122 |+                          end_, ec, t);
```

> Username: vinniefalco  
> Created_at: 2022-03-29 02:23:46 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r836997781  

\sigh this weird formatting, I can't help but think it is your IDE


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-03-29 08:32:24 UTC  
> Updated_at: 2022-03-29 15:57:15 UTC  
> Url: https://github.com/boostorg/url/pull/157#issuecomment-1081585678  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/157?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#157](https://codecov.io/gh/CPPAlliance/url/pull/157?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (d39c8c8) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/f6e8bceb91ea6508891392a47bfaf441ccfcf4b9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (f6e8bce) will **decrease** coverage by `0.15%`.  
> The diff coverage is `90.09%`.  
  
> :exclamation: Current head d39c8c8 differs from pull request most recent head dbeefc9. Consider uploading reports for the commit dbeefc9 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/157/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/157?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #157      +/-   ##  
===========================================  
- Coverage    96.80%   96.64%   -0.16%       
===========================================  
  Files          110      110                
  Lines         6201     6234      +33       
===========================================  
+ Hits          6003     6025      +22       
- Misses         198      209      +11       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/157?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/segments\_encoded.ipp](https://codecov.io/gh/CPPAlliance/url/pull/157/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c19lbmNvZGVkLmlwcA==) | `89.62% <86.90%> (-10.38%)` | :arrow_down: |  
| [include/boost/url/impl/segments\_encoded.hpp](https://codecov.io/gh/CPPAlliance/url/pull/157/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c19lbmNvZGVkLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/url.ipp](https://codecov.io/gh/CPPAlliance/url/pull/157/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmwuaXBw) | `98.23% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/157?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/157?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [f6e8bce...dbeefc9](https://codecov.io/gh/CPPAlliance/url/pull/157?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 15:59:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-924870321  

📁 include/boost/url/detail/segments_encoded_iterator_base.hpp

```diff
  95 |+     }
  96 |+ 
  97 |+     segments_encoded_iterator_base
```

> Username: vinniefalco  
> Created_at: 2022-03-29 15:59:16 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837649087  

hmm no, this is not ok - the return type has to be the derived class


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 16:00:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-924872099  

📁 include/boost/url/detail/segments_encoded_iterator_base.hpp

```diff
  40 |+ 
  41 |+ public:
  42 |+     using value_type = std::string;
```

> Username: vinniefalco  
> Created_at: 2022-03-29 16:00:26 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837650305  

You need to be careful with this. `value_type` is part of the public interface of the class, but the class itself is unutterable (since it is an implementation detail). Thus it cannot be documented. Now, you can get off on a technicality because we don't document the iterator (we just leave it as `__see_below__`). But this doesn't look right.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 21:44:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925271928  

📁 include/boost/url/detail/impl/segments_encoded_iterator_base.ipp

```diff
   9 |+ //
  10 |+ 
  11 |+ #ifndef BOOST_URL_DETAIL_IMPL_SEGMENTS_ENCODED_ITERATOR_BASE_IPP
```

> Username: vinniefalco  
> Created_at: 2022-03-29 21:44:40 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837936030  

well the name of the macro doesn't match the path, should be  
`BOOST_URL_DETAIL_SEGMENTS_ENCODED_ITERATOR_BASE_IPP`

> Username: alandefreitas  
> Created_at: 2022-03-29 21:47:47 UTC  
> Updated_at: 2022-03-29 21:48:07 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837937939  

But the path _is_ `impl`. All files in this directory start with `BOOST_URL_DETAIL_IMPL_`


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 21:44:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925272156  

📁 include/boost/url/detail/impl/segments_encoded_iterator_base.ipp

```diff
  33 |+     }
  34 |+     auto const n =
  35 |+         detail::path_prefix(s);
```

> Username: vinniefalco  
> Created_at: 2022-03-29 21:44:56 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837936191  

you're already in `detail::`


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 21:48:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925275211  

📁 include/boost/url/detail/impl/segments_iterator_base.ipp

```diff
  10 |+ 
  11 |+ #ifndef BOOST_URL_DETAIL_IMPL_SEGMENTS_ITERATOR_BASE_IPP
  12 |+ #define BOOST_URL_DETAIL_IMPL_SEGMENTS_ITERATOR_BASE_IPP
```

> Username: vinniefalco  
> Created_at: 2022-03-29 21:48:46 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837938504  

macro name

> Username: alandefreitas  
> Created_at: 2022-03-29 21:51:14 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837939921  

But the path is `impl`. All files in this directory start with `BOOST_URL_DETAIL_IMPL_`.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 21:50:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925276552  

📁 include/boost/url/impl/segments.hpp

```diff
  33 |-     std::size_t i_ = 0;
  34 |-     const_string::factory a_;
  34 |+     detail::segments_iterator_base impl_;
```

> Username: vinniefalco  
> Created_at: 2022-03-29 21:50:35 UTC  
> Updated_at: 2022-03-29 21:50:36 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837939551  

if you are going to use composition then don't call them bases! e.g. `segments_iter_impl`


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 21:51:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925277268  

📁 include/boost/url/detail/segments_encoded_iterator_base.hpp

```diff
  39 |+ 
  40 |+     using value_type = std::string;
  41 |+     using reference = string_view;
```

> Username: vinniefalco  
> Created_at: 2022-03-29 21:51:29 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837940059  

Well there's no point to having any of these nested types because no one is going to utter them


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 21:52:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925277722  

📁 include/boost/url/detail/segments_encoded_iterator_base.hpp

```diff
  56 |+     operator*() const noexcept
  57 |+     {
  58 |+         return s_;
```

> Username: vinniefalco  
> Created_at: 2022-03-29 21:52:03 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837940420  

there's no point to this function, you should just `return s_` in the enclosing class


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 21:53:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925278817  

📁 include/boost/url/detail/segments_encoded_iterator_base.hpp

```diff
  69 |+     bool
  70 |+     operator==(
  71 |+         segments_encoded_iterator_base other) const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-03-29 21:53:30 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837941242  

You're making a copy of a structure which has the size of several pointers, and besides that there is no point to this function when you could just do it in the enclosing class. Or if that bothers you, then you can make it a non-operator:  
```  
bool equal( segments_encoded_iter_impl const& other );  
```  
  
You don't need `not_equal`


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 22:02:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925285447  

📁 include/boost/url/detail/impl/segments_encoded_iterator_impl.ipp

```diff
  10 |+ 
  11 |+ #ifndef BOOST_URL_DETAIL_IMPL_SEGMENTS_ENCODED_ITERATOR_BASE_IPP
  12 |+ #define BOOST_URL_DETAIL_IMPL_SEGMENTS_ENCODED_ITERATOR_BASE_IPP
```

> Username: vinniefalco  
> Created_at: 2022-03-29 22:02:34 UTC  
> Updated_at: 2022-03-29 22:02:35 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837946308  

macro doesn't match


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 22:03:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925286147  

📁 include/boost/url/detail/impl/segments_encoded_iterator_impl.ipp

```diff
 140 |+ } // boost
 141 |+ 
 142 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2022-03-29 22:03:15 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837946714  

missing newline


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 22:03:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925286502  

📁 include/boost/url/detail/impl/segments_iterator_impl.ipp

```diff
 149 |+ } // boost
 150 |+ 
 151 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2022-03-29 22:03:47 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837946977  

missing newline


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 22:04:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925287241  

📁 include/boost/url/impl/segments.hpp

```diff
  99 |-         return a.u_ == b.u_ &&
 100 |-             a.i_ == b.i_;
  74 |+         return *impl_;
```

> Username: vinniefalco  
> Created_at: 2022-03-29 22:04:50 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837947563  

unless the type is user-facing you shouldn't use the operators. Just use `impl_.dereference()`.


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 22:57:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925287513  

📁 include/boost/url/detail/impl/segments_encoded_iterator_impl.ipp

```diff
  10 |+ 
  11 |+ #ifndef BOOST_URL_DETAIL_IMPL_SEGMENTS_ENCODED_ITERATOR_IMPL_IPP
  12 |+ #define BOOST_URL_DETAIL_IMPL_SEGMENTS_ENCODED_ITERATOR_IMPL_IPP
```

> Username: vinniefalco  
> Created_at: 2022-03-29 22:57:08 UTC  
> Updated_at: 2022-03-29 22:57:12 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837973232  

the macro doesn't match the file


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-29 23:07:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/157#pullrequestreview-925330398  

📁 include/boost/url/impl/segments.hpp

```diff
  70 | 
  88 |-     BOOST_URL_DECL
  71 |     const_string
```

> Username: vinniefalco  
> Created_at: 2022-03-29 23:07:06 UTC  
> Url: https://github.com/boostorg/url/pull/157#discussion_r837978893  

Here I think we want `reference`?


---
