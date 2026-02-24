# #220 Fix `operator->` for `local_iterator`s [Closed]

> Username: vslashg  
> Created at: 2023-11-07 20:14:21 UTC  
> Updated at: 2023-11-17 07:52:27 UTC  
> Closed at: 2023-11-17 07:52:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/220  

Invoking this operator causes a compile break.  This appears to have been caused by b1a9cde69.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-11-07 23:00:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/220#issuecomment-1800337422  

What kind of compile break does it cause?

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-11-08 00:28:34 UTC  
> Url: https://github.com/boostorg/unordered/pull/220#issuecomment-1800729182  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/220?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#220](https://app.codecov.io/gh/boostorg/unordered/pull/220?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6cdf745) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/1c0e54ee3eced7f5fe29d70316d13899af993a2d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1c0e54e) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/220/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/220?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #220   +/-   ##  
========================================  
  Coverage    98.07%   98.07%             
========================================  
  Files          143      143             
  Lines        19695    19705   +10       
========================================  
+ Hits         19316    19326   +10       
  Misses         379      379             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/unordered/pull/220?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/fca.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/220?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZjYS5ocHA=) | `99.66% <100.00%> (+<0.01%)` | :arrow_up: |  
| [test/unordered/compile\_tests.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/220?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvY29tcGlsZV90ZXN0cy5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/220?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/220?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1c0e54e...6cdf745](https://app.codecov.io/gh/boostorg/unordered/pull/220?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: vslashg  
> Created_at: 2023-11-08 14:24:40 UTC  
> Url: https://github.com/boostorg/unordered/pull/220#issuecomment-1801993255  

`lit->first` fails to compile; `operator->` for local iterators attempts to return a pointer to the node rather than a pointer to a value, so any attempt to instantiate this method (e.g by using `->` to dereference a `local_iterator`) fails to compile due to an impossible pointer conversion.  
  
The PR contains a new regression test that will demonstrate the problem.

---

## Comment 4

> Username: joaquintides  
> Created_at: 2023-11-08 14:57:35 UTC  
> Url: https://github.com/boostorg/unordered/pull/220#issuecomment-1802057800  

Hi @vslashg, we've confirmed the bug you've spotted. The fix will arrive in Boost 1.84 (but after Boost 1.84, beta, which is due today). Thank you!

---

## Comment 5

> Username: cmazakas  
> Created_at: 2023-11-08 18:29:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/220#issuecomment-1802430979  

Thank you for updating the tests!  
  
The fix seems more complicated than it needs to be.  
  
Why not just use:  
```cpp  
pointer operator->() const noexcept  
{  
  return std::addressof(dereference());  
}  
```  
?  
  
Also, we need to add coverage for `.cbegin()` so we test the `const_local_iterator` arm as well. It's fine to only update the unordered_map_test here but I'd like to see some coverage for the `unordered_set_test` as well.  
  
Personally, what I'd like to do here is update the `bucket_tests.cpp` file. You don't necessarily have to do a whole new test case, you can just update the existing one there. But I'd really like to see some tests that test more than just "compile fine" which is what the compile_tests are for.  
  
I can get this done but I wanted to offer the chance to iterate on the PR.

---

## Comment 6

> Username: vslashg  
> Created_at: 2023-11-09 14:18:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/220#issuecomment-1803919637  

I'm certainly willing to make the changes you suggest, but for such a small change it may make more sense for you to implement the fix.  Let me know what you prefer; I'm happy to see this fixed either way.

---

## Comment 7

> Username: cmazakas  
> Created_at: 2023-11-09 15:04:40 UTC  
> Updated_at: 2023-11-09 15:05:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/220#issuecomment-1804004954  

> but for such a small change it may make more sense for you to implement the fix.  
  
Sure. This is an opportunity to become a contributor though! I wouldn't pass it up.  
  
For the tests, I think we'd cover our bases if we just proved that `local_it.operator->()` yields the same address as `*local_it`.  The test suite already tests dereferencing the local iterators in other places like `invariants.hpp` so we don't need to go overboard here, just that `operator->()` is well-formed and returns the correct pointer value.  
  
Feel free to ask me any questions about how to run b2 or anything else like that.

---

## Comment 8

> Username: vslashg  
> Created_at: 2023-11-09 17:00:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/220#issuecomment-1804209096  

The friction of coming up with a test object such that `set_local_it->member` was well-formed was what discouraged me from writing a set test.  If you're happy with the test using explicit syntax of `set_local_it.operator->()` as a test invocation then things are much simpler.  
  
I should be able to get to this either today or tomorrow.  Thanks!

---

## Comment 9

> Username: joaquintides  
> Created_at: 2023-11-17 07:52:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/220#issuecomment-1815891702  

Problem fixed in #221.

---
