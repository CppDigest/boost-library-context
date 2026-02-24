# #117 Refactor tests and fix possible overflow in ICU case conversion [Merged]

> Username: Flamefire  
> Created at: 2022-10-11 09:28:17 UTC  
> Updated at: 2022-10-15 05:54:35 UTC  
> Merged at: 2022-10-15 05:54:31 UTC  
> Closed at: 2022-10-15 05:54:31 UTC  
> Url: https://github.com/boostorg/locale/pull/117  

- Refactor tests and remove/combine/handle special cases for some ICU versions in tests  
  
Prepares for adding better tests for basic_format  
  
- Fix possible overflow in ICU (case) conversion  
- Fix loss of precision when getting the time from a timepoint (conversion from `double` to `posix_time` to `double`)  
- Avoid the bug in the time conversion resulting in always zero nanoseconds resulting from wrong conversion from seconds to nanoseconds  
  
Extract the size type used by the ICU function from its signature and cast the parameters after checking the range.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-11 18:14:37 UTC  
> Updated_at: 2022-10-14 14:04:29 UTC  
> Url: https://github.com/boostorg/locale/pull/117#issuecomment-1275091844  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/117?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#117](https://codecov.io/gh/boostorg/locale/pull/117?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3a24b94) into [develop](https://codecov.io/gh/boostorg/locale/commit/7fb7ec50969d3ebf836c4f3713edcf9d6ac16b8e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7fb7ec5) will **increase** coverage by `0.21%`.  
> The diff coverage is `94.09%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/117/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #117      +/-   ##  
===========================================  
+ Coverage    88.19%   88.40%   +0.21%       
===========================================  
  Files          100      100                
  Lines         8894     8964      +70       
===========================================  
+ Hits          7844     7925      +81       
+ Misses        1050     1039      -11       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/boundary/index.hpp](https://codecov.io/gh/boostorg/locale/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvaW5kZXguaHBw) | `98.76% <ø> (ø)` | |  
| [include/boost/locale/date\_time\_facet.hpp](https://codecov.io/gh/boostorg/locale/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGF0ZV90aW1lX2ZhY2V0LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/generator.hpp](https://codecov.io/gh/boostorg/locale/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJhdG9yLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/utf.hpp](https://codecov.io/gh/boostorg/locale/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRmLmhwcA==) | `98.72% <ø> (ø)` | |  
| [include/boost/locale/util.hpp](https://codecov.io/gh/boostorg/locale/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC5ocHA=) | `10.00% <ø> (ø)` | |  
| [src/boost/locale/shared/mo\_hash.hpp](https://codecov.io/gh/boostorg/locale/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbW9faGFzaC5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/util/codecvt\_converter.cpp](https://codecov.io/gh/boostorg/locale/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2NvZGVjdnRfY29udmVydGVyLmNwcA==) | `92.64% <ø> (ø)` | |  
| [test/test\_collate.cpp](https://codecov.io/gh/boostorg/locale/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbGxhdGUuY3Bw) | `97.18% <ø> (ø)` | |  
| [test/test\_convert.cpp](https://codecov.io/gh/boostorg/locale/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbnZlcnQuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_winapi\_collate.cpp](https://codecov.io/gh/boostorg/locale/pull/117/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3dpbmFwaV9jb2xsYXRlLmNwcA==) | `88.40% <ø> (ø)` | |  
| ... and [23 more](https://codecov.io/gh/boostorg/locale/pull/117/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/117?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/117?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7fb7ec5...3a24b94](https://codecov.io/gh/boostorg/locale/pull/117?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
