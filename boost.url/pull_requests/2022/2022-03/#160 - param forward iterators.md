# #160 param forward iterators [Merged]

> Username: alandefreitas  
> Created at: 2022-03-31 18:54:12 UTC  
> Updated at: 2022-06-08 13:37:48 UTC  
> Merged at: 2022-04-04 17:02:31 UTC  
> Closed at: 2022-04-04 17:02:32 UTC  
> Url: https://github.com/boostorg/url/pull/160  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-31 19:01:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928051416  

📁 include/boost/url/detail/impl/params_encoded_iterator_impl.ipp

```diff
  10 |+ 
  11 |+ #ifndef BOOST_URL_DETAIL_IMPL_PARAMS_ENCODED_ITERATOR_IMPL_IPP
  12 |+ #define BOOST_URL_DETAIL_IMPL_PARAMS_ENCODED_ITERATOR_IMPL_IPP
```

> Username: vinniefalco  
> Created_at: 2022-03-31 19:01:56 UTC  
> Updated_at: 2022-03-31 19:01:57 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839931158  

the macro name again doesn't match the file...

> Username: alandefreitas  
> Created_at: 2022-03-31 19:03:36 UTC  
> Updated_at: 2022-03-31 19:03:37 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839932422  

What should the macro be?  
  
`boost/url/detail/impl/params_encoded_iterator_impl.ipp`  
`BOOST_URL_DETAIL_IMPL_PARAMS_ENCODED_ITERATOR_IMPL_IPP`

> Username: vinniefalco  
> Created_at: 2022-03-31 19:07:25 UTC  
> Updated_at: 2022-03-31 19:07:26 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839935039  

hmm you're right


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-31 19:02:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928051833  

📁 include/boost/url/detail/impl/params_encoded_iterator_impl.ipp

```diff
  26 |+     if(pos_ != begin_ || i_ != 0)
  27 |+     {
  28 |+         BOOST_ASSERT(
```

> Username: vinniefalco  
> Created_at: 2022-03-31 19:02:21 UTC  
> Updated_at: 2022-03-31 19:02:22 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839931463  

you need to include boost/assert.hpp if you are going to use this macro


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-31 19:07:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928057454  

📁 include/boost/url/detail/impl/params_encoded_iterator_impl.ipp

```diff
  77 |+ increment() noexcept
  78 |+ {
  79 |+     // valid domain
```

> Username: vinniefalco  
> Created_at: 2022-03-31 19:07:50 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839935305  

what is this nonsense comment


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-31 19:08:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928057650  

📁 include/boost/url/detail/impl/params_encoded_iterator_impl.ipp

```diff
  81 |+     BOOST_ASSERT(end_ != nullptr);
  82 |+ 
  83 |+     // cannot increment one past end
```

> Username: vinniefalco  
> Created_at: 2022-03-31 19:08:01 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839935427  

the assert speaks for itself, the comment adds no information


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-31 19:09:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928059137  

📁 include/boost/url/detail/impl/params_encoded_iterator_impl.ipp

```diff
 164 |+     BOOST_ASSERT(pos_ != end_);
 165 |+ 
 166 |+     // cannot dereference one before begin
```

> Username: vinniefalco  
> Created_at: 2022-03-31 19:09:28 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839936502  

one before begin? what is that?

> Username: alandefreitas  
> Created_at: 2022-03-31 19:33:55 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839953895  

std::prev(p.begin())

> Username: vinniefalco  
> Created_at: 2022-03-31 19:49:35 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839964740  

Oh, because bidirectional

> Username: vinniefalco  
> Created_at: 2022-03-31 19:50:01 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839965062  

Wait, no,... the iterator should never be in the one-before-begin state


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-31 19:10:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928059720  

📁 include/boost/url/detail/impl/params_iterator_impl.ipp

```diff
  93 |+     BOOST_ASSERT(pos_ != nullptr);
  94 |+ 
  95 |+     using reference = params_view::reference;
```

> Username: vinniefalco  
> Created_at: 2022-03-31 19:10:05 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839936932  

this isn't needed


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-31 19:11:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928061232  

📁 include/boost/url/detail/impl/params_iterator_impl.ipp

```diff
 133 |+         return;
 134 |+     }
 135 |+     else
```

> Username: vinniefalco  
> Created_at: 2022-03-31 19:11:33 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839937996  

if you're always returning in the `if` you don't need the else


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-31 19:12:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928062436  

📁 include/boost/url/detail/params_encoded_iterator_impl.hpp

```diff
  67 |+     BOOST_URL_DECL
  68 |+     bool
  69 |+     equal(
```

> Username: vinniefalco  
> Created_at: 2022-03-31 19:12:44 UTC  
> Updated_at: 2022-03-31 19:12:45 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839938846  

should we just make the definition inline? It is 1-liner...


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-31 19:16:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928065731  

📁 include/boost/url/detail/params_iterator_impl.hpp

```diff
  32 |+     void scan() noexcept;
  33 |+ 
  34 |+     BOOST_URL_DECL
```

> Username: vinniefalco  
> Created_at: 2022-03-31 19:16:05 UTC  
> Updated_at: 2022-03-31 19:16:06 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839941162  

does this need to be exported?

> Username: alandefreitas  
> Created_at: 2022-03-31 19:46:41 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839962688  

It failed in CI when I didn't export it. I believe because the other iterator is inlined and calling it.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-31 19:34:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928084853  

📁 include/boost/url/impl/params.hpp

```diff
  95 |+         params_view::reference r0 =
  96 |+             impl_.dereference();
  97 |         return {
```

> Username: vinniefalco  
> Created_at: 2022-03-31 19:34:44 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839954439  

This doesn't look right, you should be able to just write `return impl_.dereference()`

> Username: alandefreitas  
> Created_at: 2022-03-31 19:49:57 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839965003  

`params_view::iterator` and `params::iterator` use different types. We can either make this conversion or make `params::reference` be an alias to `params_view::reference`.

> Username: vinniefalco  
> Created_at: 2022-04-01 00:41:55 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r840132436  

If they use different types then they can't share the `dereference` code can they

> Username: alandefreitas  
> Created_at: 2022-04-01 00:46:49 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r840136144  

The logic is 99% the same. They are different types with exactly the same members. It's just a matter of converting `params_view::reference` to `params::reference`.  
  
We can  
  
- make this conversion where it is,  
- make this conversion in another one-line dereference function, or  
- make params::reference just be an alias to params_view::reference, since they have the exact members


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-31 19:39:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928089328  

📁 include/boost/url/impl/params.hpp

```diff
 191 | auto
 192 | params::
 193 | front() const ->
```

> Username: vinniefalco  
> Created_at: 2022-03-31 19:39:23 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r839957539  

I don't think we need front and back for params


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 00:43:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928323459  

📁 include/boost/url/detail/params_iterator_impl.hpp

```diff
  44 |+         std::size_t nparam,
  45 |+         int,
  46 |+         const_string::factory a) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-04-01 00:43:51 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r840133752  

should `a` be `const&`?


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-01 00:45:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-928324097  

📁 include/boost/url/detail/params_iterator_impl.hpp

```diff
  67 |+     BOOST_URL_DECL
  68 |+     void
  69 |+     decrement() noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-04-01 00:45:28 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r840135051  

This no longer exists


---

## Comment 14

> Username: codecov[bot]  
> Created_at: 2022-04-01 02:06:27 UTC  
> Updated_at: 2022-04-04 15:05:19 UTC  
> Url: https://github.com/boostorg/url/pull/160#issuecomment-1085329584  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/160?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#160](https://codecov.io/gh/CPPAlliance/url/pull/160?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (52534c9) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/d5dbea53e731feaa1d1836baf9a90b2347138f26?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (d5dbea5) will **decrease** coverage by `0.39%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 52534c9 differs from pull request most recent head 3b6e2af. Consider uploading reports for the commit 3b6e2af to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/160/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #160      +/-   ##  
===========================================  
- Coverage    96.75%   96.35%   -0.40%       
===========================================  
  Files          115      118       +3       
  Lines         6102     6071      -31       
===========================================  
- Hits          5904     5850      -54       
- Misses         198      221      +23       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [...url/detail/impl/segments\_encoded\_iterator\_impl.ipp](https://codecov.io/gh/CPPAlliance/url/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvc2VnbWVudHNfZW5jb2RlZF9pdGVyYXRvcl9pbXBsLmlwcA==) | `94.36% <ø> (ø)` | |  
| [...e/boost/url/detail/impl/segments\_iterator\_impl.ipp](https://codecov.io/gh/CPPAlliance/url/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvc2VnbWVudHNfaXRlcmF0b3JfaW1wbC5pcHA=) | `96.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments.hpp](https://codecov.io/gh/CPPAlliance/url/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50cy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/rfc/scheme\_rule.hpp](https://codecov.io/gh/CPPAlliance/url/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL3NjaGVtZV9ydWxlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [...t/url/detail/impl/params\_encoded\_iterator\_impl.ipp](https://codecov.io/gh/CPPAlliance/url/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcGFyYW1zX2VuY29kZWRfaXRlcmF0b3JfaW1wbC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [...ude/boost/url/detail/impl/params\_iterator\_impl.ipp](https://codecov.io/gh/CPPAlliance/url/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcGFyYW1zX2l0ZXJhdG9yX2ltcGwuaXBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/url/detail/params\_encoded\_iterator\_impl.hpp](https://codecov.io/gh/CPPAlliance/url/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3BhcmFtc19lbmNvZGVkX2l0ZXJhdG9yX2ltcGwuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/params\_iterator\_impl.hpp](https://codecov.io/gh/CPPAlliance/url/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3BhcmFtc19pdGVyYXRvcl9pbXBsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| ... and [21 more](https://codecov.io/gh/CPPAlliance/url/pull/160/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/160?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/160?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [d5dbea5...3b6e2af](https://codecov.io/gh/CPPAlliance/url/pull/160?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-04 16:29:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-930657088  

📁 include/boost/url/detail/impl/params_iterator_impl.ipp

```diff
  25 |+     string_view s(pos_, end_ - pos_);
  26 |+     std::size_t i;
  27 |+     if(pos_ != begin_ || i_ != 0)
```

> Username: vinniefalco  
> Created_at: 2022-04-04 16:29:29 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r841932348  

two lines please

> Username: vinniefalco  
> Created_at: 2022-04-04 16:48:45 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r841949069  

How can only one of these conditions be true?


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-04 16:50:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/160#pullrequestreview-930681861  

📁 include/boost/url/detail/params_encoded_iterator_impl.hpp

```diff
  30 |+ 
  31 |+     BOOST_URL_DECL
  32 |+     void scan() noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-04-04 16:50:03 UTC  
> Updated_at: 2022-04-04 16:50:04 UTC  
> Url: https://github.com/boostorg/url/pull/160#discussion_r841950154  

Does this need to be exported?


---
