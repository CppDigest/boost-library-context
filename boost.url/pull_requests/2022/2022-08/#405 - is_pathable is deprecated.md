# #405 is_pathable is deprecated [Merged]

> Username: alandefreitas  
> Created at: 2022-08-15 18:27:36 UTC  
> Updated at: 2022-08-30 21:04:11 UTC  
> Merged at: 2022-08-16 02:23:06 UTC  
> Closed at: 2022-08-16 02:23:06 UTC  
> Url: https://github.com/boostorg/url/pull/405  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-15 19:00:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/405#pullrequestreview-1073069353  

📁 test/unit/snippets.cpp

```diff
 152 |         for (auto seg: u.segments())
 153 |-             p /= seg;
 153 |+             p.append(seg.begin(), seg.end());
```

> Username: vinniefalco  
> Created_at: 2022-08-15 19:00:17 UTC  
> Url: https://github.com/boostorg/url/pull/405#discussion_r946038528  

But this is not what we talked about. Can't we have `seg.append_to( p )`?

> Username: alandefreitas  
> Created_at: 2022-08-15 19:52:00 UTC  
> Updated_at: 2022-08-15 20:01:56 UTC  
> Url: https://github.com/boostorg/url/pull/405#discussion_r946074309  

Not anymore. Because of what Peter said later on.  
We also included the requirement that the `value_type` of a `mutable_string` should be `char`.  
So this will not work on windows.  
  
So this would only work on windows if we changed that to a `value_type` convertible from `char`, which we discussed after `seg.append_to(...)`.  
Peter was very much against it and said `mutable_string` is correct as it is. And his argument convinced me.  
  
Basically, his argument is that  
  
> if you remove it, things like wstring and u16string will become mutable strings, but the semantics will probably be wrong  
> because they will convert char by char, without regards of encoding

> Username: vinniefalco  
> Created_at: 2022-08-15 19:57:11 UTC  
> Updated_at: 2022-08-15 19:57:12 UTC  
> Url: https://github.com/boostorg/url/pull/405#discussion_r946077710  

Yes `char` value type is correct for us.


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2022-08-15 19:00:35 UTC  
> Updated_at: 2022-08-15 19:08:58 UTC  
> Url: https://github.com/boostorg/url/pull/405#issuecomment-1215615732  

Where's all the tests we talked about with std filesystem and boost?

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-08-15 19:06:47 UTC  
> Updated_at: 2022-08-15 19:10:07 UTC  
> Url: https://github.com/boostorg/url/pull/405#issuecomment-1215621900  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/405?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#405](https://codecov.io/gh/CPPAlliance/url/pull/405?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (6adb381) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/cba301383791f67c31b3ccd52a401849f7cf8940?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (cba3013) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/405/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/405?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #405   +/-   ##  
========================================  
  Coverage    97.89%   97.89%             
========================================  
  Files          132      132             
  Lines         6281     6281             
========================================  
  Hits          6149     6149             
  Misses         132      132             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/405?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/pct\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/405/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X2VuY29kZWRfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/405?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/405?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [cba3013...6adb381](https://codecov.io/gh/CPPAlliance/url/pull/405?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-08-15 19:54:49 UTC  
> Url: https://github.com/boostorg/url/pull/405#issuecomment-1215716345  

> Where's all the tests we talked about with std filesystem and boost?  
  
There's nothing to test because we are just removing a feature. Not including one.   
Neither `p.append(s)` nor `s.append_to(p)` are possible or desirable.  
  
So what this PR does is just reformulating the example that works with the correct filesystem API because boost 1.80 broke what we had.

---
