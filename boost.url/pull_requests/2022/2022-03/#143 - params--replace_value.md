# #143 params::replace_value [Closed]

> Username: alandefreitas  
> Created at: 2022-03-16 15:40:51 UTC  
> Updated at: 2022-03-21 16:56:54 UTC  
> Closed at: 2022-03-18 22:14:34 UTC  
> Url: https://github.com/boostorg/url/pull/143  

fix #59

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-03-16 15:41:35 UTC  
> Url: https://github.com/boostorg/url/pull/143#issuecomment-1069272696  

This looks too simple. Do we need some optimization I'm missing here?

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-16 16:02:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/143#pullrequestreview-911896979  

📁 include/boost/url/impl/params.ipp

```diff
 172 |+     return emplace_at(
 173 |+         pos,
 174 |+         (*pos).key,
```

> Username: vinniefalco  
> Created_at: 2022-03-16 16:02:30 UTC  
> Updated_at: 2022-03-16 16:02:31 UTC  
> Url: https://github.com/boostorg/url/pull/143#discussion_r828188375  

well what if the implementation reallocates, what happens to `(*pos).key`?

> Username: vinniefalco  
> Created_at: 2022-03-16 16:05:03 UTC  
> Updated_at: 2022-03-16 16:05:04 UTC  
> Url: https://github.com/boostorg/url/pull/143#discussion_r828191194  

Ah, `(*pos).key` allocates memory. We don't want to do that.

> Username: alandefreitas  
> Created_at: 2022-03-16 17:40:30 UTC  
> Url: https://github.com/boostorg/url/pull/143#discussion_r828286085  

> Ah, (*pos).key allocates memory. We don't want to do that.  
  
Yes. This is a problem.  
  
> well what if the implementation reallocates, what happens to (*pos).key?  
  
Isn't that something we should consider for other functions too, like `emplace_at`?

> Username: vinniefalco  
> Created_at: 2022-03-17 16:00:33 UTC  
> Url: https://github.com/boostorg/url/pull/143#discussion_r829221955  

> Isn't that something we should consider for other functions too, like emplace_at?  
  
Yes, most functions which accept string views need to use `copied_strings`.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-16 16:03:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/143#pullrequestreview-911898204  

📁 test/unit/params.cpp

```diff
 229 |+             url u = parse_uri_reference(
 230 |+                         "/?k0=0&k%31=1&k2=#f").value();
 231 |+             params p = u.params(pa_.allocator());
```

> Username: vinniefalco  
> Created_at: 2022-03-16 16:03:18 UTC  
> Url: https://github.com/boostorg/url/pull/143#discussion_r828189321  

where's the `params_encoded` implementation?


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-03-16 19:07:51 UTC  
> Updated_at: 2022-03-18 18:23:12 UTC  
> Url: https://github.com/boostorg/url/pull/143#issuecomment-1069507011  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/143?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#143](https://codecov.io/gh/CPPAlliance/url/pull/143?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (6e26b57) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/999dbe147be213da73891ff71bfda0a290199aad?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (999dbe1) will **increase** coverage by `0.09%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 6e26b57 differs from pull request most recent head 2804061. Consider uploading reports for the commit 2804061 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/143/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/143?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #143      +/-   ##  
===========================================  
+ Coverage    96.71%   96.81%   +0.09%       
===========================================  
  Files          110      110                
  Lines         6153     6153                
===========================================  
+ Hits          5951     5957       +6       
+ Misses         202      196       -6       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/143?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/impl/any\_query\_iter.ipp](https://codecov.io/gh/CPPAlliance/url/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3F1ZXJ5X2l0ZXIuaXBw) | `96.85% <100.00%> (ø)` | |  
| [include/boost/url/impl/params.ipp](https://codecov.io/gh/CPPAlliance/url/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXMuaXBw) | `100.00% <100.00%> (+1.94%)` | :arrow_up: |  
| [include/boost/url/impl/params\_encoded.ipp](https://codecov.io/gh/CPPAlliance/url/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZC5pcHA=) | `100.00% <100.00%> (+2.29%)` | :arrow_up: |  
| [include/boost/url/impl/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXMuaHBw) | `100.00% <0.00%> (+0.62%)` | :arrow_up: |  
| [include/boost/url/impl/params\_encoded.hpp](https://codecov.io/gh/CPPAlliance/url/pull/143/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZC5ocHA=) | `100.00% <0.00%> (+0.64%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/143?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/143?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [999dbe1...2804061](https://codecov.io/gh/CPPAlliance/url/pull/143?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2022-03-17 15:40:25 UTC  
> Url: https://github.com/boostorg/url/pull/143#issuecomment-1070991591  

Aren't `std::random_access_iterator_tag` iterators supposed to access elements in constant-time? Or is the current implementation just a placeholder?  
  
The way `params_encoded::iterator` is implemented, it has linear cost to access elements even if we use it as a `std::bidirectional_iterator`. The only thing happening in constant time is `operator+`.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-03-17 15:58:45 UTC  
> Url: https://github.com/boostorg/url/pull/143#issuecomment-1071013549  

The current implementation is a placeholder

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2022-03-17 19:59:41 UTC  
> Url: https://github.com/boostorg/url/pull/143#issuecomment-1071323887  

Leaving the issues with `copied_strings` apart, reusing the logic from `emplace_at` in `params_encoded::replace_value` works as fine as `emplace_at`. No copies.  
  
However, `params::replace_value` is more complicated than that. We can avoid the copy with   
  
```  
auto const r =  
        u_->param(pos.i_);  
    string_view key{  
        u_->s_ + r.pos + 1,  
        r.nk - 1};  
```  
  
as usual, but the function will attempt to reencode the key whenever it's already encoded. The obvious solutions sound bad, like reimplementing everything in `edit_params`, or another `any_query_iter` just for that.  
  
Suggestions?

---

## Comment 8

> Username: alandefreitas  
> Created_at: 2022-03-18 17:25:28 UTC  
> Url: https://github.com/boostorg/url/pull/143#issuecomment-1072629120  

So I'll go with a `replace_value_iter`.

---

## Review 9 [Commented]

> Username: alandefreitas  
> Created_at: 2022-03-18 19:12:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/143#pullrequestreview-914778828  

📁 include/boost/url/detail/impl/any_query_iter.ipp

```diff
 223 |         key.data() != nullptr)
 224 |     {
 223 |-         std::size_t n =
```

> Username: alandefreitas  
> Created_at: 2022-03-18 19:12:22 UTC  
> Updated_at: 2022-03-18 19:12:59 UTC  
> Url: https://github.com/boostorg/url/pull/143#discussion_r830287042  

This snippet is here for correctness in case we ever use the iterator differently, but it's [impossible to cover](https://codecov.io/github/CPPAlliance/url/commit/2804061fa80425fd6f5e672f3f3663793d616fa3) this line indirectly through `replace_value`. Is there anything, in particular, we should do here?

> Username: vinniefalco  
> Created_at: 2022-03-18 21:30:24 UTC  
> Updated_at: 2022-03-18 21:30:25 UTC  
> Url: https://github.com/boostorg/url/pull/143#discussion_r830362329  

if you remove the snippet here you should also remove it in the other places. you could just assert that data() is not null.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-18 21:28:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/143#pullrequestreview-914880861  

📁 include/boost/url/detail/impl/any_query_iter.ipp

```diff
 327 |+     {
 328 |+         static constexpr auto cs =
 329 |+             pchars + '/' + '?';
```

> Username: vinniefalco  
> Created_at: 2022-03-18 21:28:38 UTC  
> Url: https://github.com/boostorg/url/pull/143#discussion_r830361547  

tsk tsk duplicating the charset


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-18 22:07:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/143#pullrequestreview-914901410  

📁 include/boost/url/detail/impl/any_query_iter.ipp

```diff
 284 |+     std::size_t& n) noexcept
 285 |+ {
 286 |+     n += key.size();
```

> Username: vinniefalco  
> Created_at: 2022-03-18 22:07:38 UTC  
> Url: https://github.com/boostorg/url/pull/143#discussion_r830377160  

but.. is `key` encoded?

> Username: alandefreitas  
> Created_at: 2022-03-18 22:15:06 UTC  
> Url: https://github.com/boostorg/url/pull/143#discussion_r830382481  

Yes. This is just the key in the underlying URL. It doesn't come from outside.


---
