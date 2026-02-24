# #1814 Add accessor function to File member of basic_file_body [Closed]

> Username: mika-fischer  
> Created at: 2020-01-21 09:01:13 UTC  
> Updated at: 2020-01-23 15:56:40 UTC  
> Closed at: 2020-01-23 15:56:40 UTC  
> Url: https://github.com/boostorg/beast/pull/1814  

Fixes #1260

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-01-21 09:51:43 UTC  
> Updated_at: 2020-01-23 09:47:36 UTC  
> Url: https://github.com/boostorg/beast/pull/1814#issuecomment-576602706  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1814?src=pr&el=h1) Report  
> Merging [#1814](https://codecov.io/gh/boostorg/beast/pull/1814?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/46ac848fa46ce5b81f2bb5b221774d15c0cdd128?src=pr&el=desc) will **decrease** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1814/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1814?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1814      +/-   ##  
===========================================  
- Coverage    95.17%   95.15%   -0.03%       
===========================================  
  Files          156      156                
  Lines        11949    11949                
===========================================  
- Hits         11373    11370       -3       
- Misses         576      579       +3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1814?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/basic\_file\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/1814/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX2ZpbGVfYm9keS5ocHA=) | `81.96% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1814/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0%> (-0.9%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1814/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0%> (-0.2%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1814/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.5% <0%> (-0.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1814?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1814?src=pr&el=footer). Last update [46ac848...5987e62](https://codecov.io/gh/boostorg/beast/pull/1814?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-22 15:38:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1814#pullrequestreview-346684866  

📁 include/boost/beast/http/basic_file_body.hpp

```diff
 112 |     value_type& operator=(value_type&& other) = default;
 113 | 
 114 |+     /// Accessor to contained File instance
```

> Username: vinniefalco  
> Created_at: 2020-01-22 15:38:45 UTC  
> Updated_at: 2020-01-23 09:34:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1814#discussion_r369634480  

Should read  
```  
/// Return the file  
```

> Username: mika-fischer  
> Created_at: 2020-01-23 09:35:33 UTC  
> Updated_at: 2020-01-23 09:35:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1814#discussion_r370012677  

Changed


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-22 15:38:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1814#pullrequestreview-346685019  

📁 include/boost/beast/http/impl/file_body_win32.hpp

```diff
  79 |         value_type& operator=(value_type&& other) = default;
  80 | 
  81 |+         /// Accessor to contained File instance
```

> Username: vinniefalco  
> Created_at: 2020-01-22 15:38:56 UTC  
> Updated_at: 2020-01-23 09:34:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1814#discussion_r369634605  

Should read  
```  
/// Return the file  
```

> Username: mika-fischer  
> Created_at: 2020-01-23 09:35:13 UTC  
> Updated_at: 2020-01-23 09:35:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1814#discussion_r370012514  

I think that one does not generate docs and has no other doc comments, so I just removed it


---

## Review 4 [Approved]

> Username: madmongo1  
> Created_at: 2020-01-23 14:26:04 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/1814#pullrequestreview-347335393  

All issues addressed. Accepting.

---
