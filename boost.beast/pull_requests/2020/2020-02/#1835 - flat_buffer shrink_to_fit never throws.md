# #1835 flat_buffer shrink_to_fit never throws [Closed]

> Username: madmongo1  
> Created at: 2020-02-04 11:36:37 UTC  
> Updated at: 2020-02-05 14:40:53 UTC  
> Closed at: 2020-02-05 14:40:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1835  

fixes #1831

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-04 11:48:33 UTC  
> Updated_at: 2020-02-04 17:23:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#issuecomment-581871216  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1835?src=pr&el=h1) Report  
> Merging [#1835](https://codecov.io/gh/boostorg/beast/pull/1835?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c9d5049307925bccaf2f5e57c127db1a1c78e178?src=pr&el=desc) will **decrease** coverage by `0.03%`.  
> The diff coverage is `33.33%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1835/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1835?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1835      +/-   ##  
===========================================  
- Coverage    95.19%   95.15%   -0.04%       
===========================================  
  Files          156      156                
  Lines        11956    11958       +2       
===========================================  
- Hits         11381    11379       -2       
- Misses         575      579       +4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1835?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/flat\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1835/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/flat\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1835/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9idWZmZXIuaHBw) | `99.14% <33.33%> (-0.86%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1835/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0%> (-0.9%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1835/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.5% <0%> (-0.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1835?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1835?src=pr&el=footer). Last update [c9d5049...307d650](https://codecov.io/gh/boostorg/beast/pull/1835?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-04 13:45:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1835#pullrequestreview-352983613  

📁 include/boost/beast/core/impl/flat_buffer.hpp

```diff
 282 |+ #ifndef BOOST_NO_EXCEPTIONS
 283 |+         }
 284 |+         catch(std::exception&)
```

> Username: vinniefalco  
> Created_at: 2020-02-04 13:45:12 UTC  
> Updated_at: 2020-02-04 17:02:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374677109  

shouldn't this be `const&`?

> Username: madmongo1  
> Created_at: 2020-02-04 14:32:36 UTC  
> Updated_at: 2020-02-04 17:02:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374704032  

I believe best practice is to catch exceptions by mutable reference. It allows the the exception to be modified and re-thrown at the catch site without slicing (since exceptions are normally caught by their base class).

> Username: vinniefalco  
> Created_at: 2020-02-04 14:43:13 UTC  
> Updated_at: 2020-02-04 17:02:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374710466  

there's no slicing, exceptions are not passed by value, and `const&` informs the reader that the exception is not modified


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-04 13:45:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1835#pullrequestreview-352983708  

📁 include/boost/beast/core/impl/flat_buffer.hpp

```diff
 265 | shrink_to_fit()
```

> Username: vinniefalco  
> Created_at: 2020-02-04 13:45:22 UTC  
> Updated_at: 2020-02-04 17:02:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374677182  

`noexcept`?

> Username: madmongo1  
> Created_at: 2020-02-04 14:33:34 UTC  
> Updated_at: 2020-02-04 17:02:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374704618  

This would change the signature of the public interface. I didn't want to do this without concensus.

> Username: vinniefalco  
> Created_at: 2020-02-04 14:41:15 UTC  
> Updated_at: 2020-02-04 17:02:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374709208  

I think we can mark it `noexcept` safely

> Username: madmongo1  
> Created_at: 2020-02-04 14:55:24 UTC  
> Updated_at: 2020-02-04 17:02:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374718263  

OK. I'll check the documentation in case it needs to mention it


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-02-04 16:07:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#issuecomment-581983391  

Commit message should read `flat_buffer shrink_to_fit is noexcept`

---

## Review 5 [Commented]

> Username: glenfe  
> Created_at: 2020-02-04 16:39:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1835#pullrequestreview-353127276  

📁 include/boost/beast/core/impl/flat_buffer.hpp

```diff
 279 |+         {
 280 |+ #endif
 281 |+             p = alloc(len);
```

> Username: glenfe  
> Created_at: 2020-02-04 16:39:37 UTC  
> Updated_at: 2020-02-04 17:02:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374786396  

Isn't it an invariant of basic_flat_buffer that size() is less than allocator's max_size()? If so, you don't need to call the checked alloc() function here if you can call allocate() directly.

> Username: vinniefalco  
> Created_at: 2020-02-04 16:43:08 UTC  
> Updated_at: 2020-02-04 17:02:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374788618  

calling `allocate()` directly is just so wordy and cumbersome that I mentally defaulted to calling the member function instead

> Username: madmongo1  
> Created_at: 2020-02-04 16:49:59 UTC  
> Updated_at: 2020-02-04 17:02:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374792882  

`alloc()` is defined in the same translation unit as its caller. I'd be stunned if the double check isn't elided by the optimiser in all honesty.

> Username: madmongo1  
> Created_at: 2020-02-04 17:04:38 UTC  
> Updated_at: 2020-02-04 17:04:39 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374801603  

resolved the comment wording. Noted the redundant test but I am reluctant to change existing logic in this commit. Is it worh opening another issue?

> Username: madmongo1  
> Created_at: 2020-02-04 17:07:39 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374803284  

...and learning to spell?

> Username: glenfe  
> Created_at: 2020-02-04 17:21:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374811129  

As I pointed out to Richard `allocator_traits` are not required for `allocate` and `deallocate`, so it's not really all that wordy if you just did: `p = get().allocate(len);`   
  
(All of the `allocate()` and `deallocate()` in this file can avoid `allocator_traits`).

> Username: vinniefalco  
> Created_at: 2020-02-04 17:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374821135  

@madmongo1 up to you if you want to make the change that Glen suggests. I wouldn't call it a change in "logic" more like a cosmetic change. Allocators are kind of annoying - Glen, not all of us have memorized all the arcane rules for when you need traits and when you don't.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-04 17:15:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1835#pullrequestreview-353154502  

📁 CHANGELOG.md

```diff
   1 |+ Version XXX:
   2 |+ 
   3 |+ * flat_buffer shrink_to_fit is noexcept
```

> Username: vinniefalco  
> Created_at: 2020-02-04 17:15:02 UTC  
> Updated_at: 2020-02-04 17:15:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1835#discussion_r374807410  

the commit message should match this text


---
