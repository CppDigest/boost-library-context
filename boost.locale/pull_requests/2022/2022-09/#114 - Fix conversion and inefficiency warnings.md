# #114 Fix conversion and inefficiency warnings [Merged]

> Username: apolukhin  
> Created at: 2022-09-17 15:56:52 UTC  
> Updated at: 2022-09-18 09:11:17 UTC  
> Merged at: 2022-09-18 09:09:58 UTC  
> Closed at: 2022-09-18 09:09:58 UTC  
> Url: https://github.com/boostorg/locale/pull/114  



---

## Review 1 [Approved]

> Username: Flamefire  
> Created_at: 2022-09-17 16:39:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/locale/pull/114#pullrequestreview-1111451964  

Thanks, looks good to me.  
  
As you mentioned "conversion and inefficiency warnings", where do those warnings come from? Might be worth enabling them for CI.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2022-09-17 18:27:28 UTC  
> Url: https://github.com/boostorg/locale/pull/114#issuecomment-1250119680  

The warnings come from clang-tidy, in particular https://clang.llvm.org/extra/clang-tidy/checks/performance/faster-string-find.html

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-09-17 19:11:13 UTC  
> Updated_at: 2022-09-17 19:58:53 UTC  
> Url: https://github.com/boostorg/locale/pull/114#issuecomment-1250125969  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/114?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#114](https://codecov.io/gh/boostorg/locale/pull/114?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7668335) into [develop](https://codecov.io/gh/boostorg/locale/commit/8c0d52fa60de756a3dae78c81ccd83aa8b296699?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8c0d52f) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/114/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/114?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #114   +/-   ##  
========================================  
  Coverage    84.20%   84.20%             
========================================  
  Files           73       73             
  Lines         5801     5801             
========================================  
  Hits          4885     4885             
  Misses         916      916             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/114?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/gnu\_gettext.hpp](https://codecov.io/gh/boostorg/locale/pull/114/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ251X2dldHRleHQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [src/boost/locale/posix/numeric.cpp](https://codecov.io/gh/boostorg/locale/pull/114/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9udW1lcmljLmNwcA==) | `71.77% <100.00%> (ø)` | |  
| [src/boost/locale/util/locale\_data.cpp](https://codecov.io/gh/boostorg/locale/pull/114/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2xvY2FsZV9kYXRhLmNwcA==) | `68.75% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/114?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/114?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8c0d52f...7668335](https://codecov.io/gh/boostorg/locale/pull/114?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 4 [Commented]

> Username: Flamefire  
> Created_at: 2022-09-17 20:49:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/locale/pull/114#pullrequestreview-1111469409  

📁 src/boost/locale/posix/numeric.cpp

```diff
 162 |     {
 163 |-         char_type fmt[4] = { '%' , modifier != 0 ? modifier : format , modifier == 0 ? '\0' : format };
 163 |+         char_type fmt[4] = { '%' , static_cast<char_type>(modifier != 0 ? modifier : format) , static_cast<char_type>(modifier == 0 ? '\0' : format) };
```

> Username: Flamefire  
> Created_at: 2022-09-17 20:49:41 UTC  
> Url: https://github.com/boostorg/locale/pull/114#discussion_r973627325  

I'm wondering: Why the explicit casts? The implicit casts seem to be just fine and it seems no cast is required for `'%'`  
  
Is there a warning triggered by this or anything? If so can you quote it?  
I'd rather prefer to keep this shorter using the implicit casts

> Username: apolukhin  
> Created_at: 2022-09-18 08:40:33 UTC  
> Url: https://github.com/boostorg/locale/pull/114#discussion_r973683512  

On ARM the sign of `char` is different and because of that compilers were giving warnings (or even refusing to compile)  
  
https://godbolt.org/z/4jn5ce7T6


---
