# #216 Fix possible type confusion with `boost::locale::collator` [Merged]

> Username: Flamefire  
> Created at: 2024-02-07 19:05:38 UTC  
> Updated at: 2024-03-14 00:41:00 UTC  
> Merged at: 2024-02-08 18:03:36 UTC  
> Closed at: 2024-02-08 18:03:36 UTC  
> Url: https://github.com/boostorg/locale/pull/216  

The class derived from `std::collate` which is always present in `std::locale`.  
So checks for the `boost::locale::collator` facet may return wrongly true.  
  
As a fix make this an independent facet with its own ID.  
Use an adapter such that a std::collate derived class can use its abilities.  
  
Add tests to detect the issue and verify the fix.  
  
Fixes #215

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-08 09:09:57 UTC  
> Updated_at: 2024-02-08 17:24:48 UTC  
> Url: https://github.com/boostorg/locale/pull/216#issuecomment-1933640967  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `4 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`941f853`)](https://app.codecov.io/gh/boostorg/locale/commit/941f853c5c35f6b55403350bece1267318b68b4d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 95.66% compared to head [(`1bd9b6c`)](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 95.71%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/216/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #216      +/-   ##  
===========================================  
+ Coverage    95.66%   95.71%   +0.05%       
===========================================  
  Files          115      116       +1       
  Lines         9885     9919      +34       
===========================================  
+ Hits          9456     9494      +38       
+ Misses         429      425       -4       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/collator.hpp](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvY29sbGF0b3IuaHBw) | `100.00% <100.00%> (ø)` | |  
| [src/boost/locale/shared/ids.cpp](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvaWRzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [src/boost/locale/shared/mo\_lambda.cpp](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbW9fbGFtYmRhLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [src/boost/locale/shared/std\_collate\_adapter.hpp](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvc3RkX2NvbGxhdGVfYWRhcHRlci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [src/boost/locale/win32/api.hpp](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS93aW4zMi9hcGkuaHBw) | `91.05% <100.00%> (+2.77%)` | :arrow_up: |  
| [test/test\_generator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dlbmVyYXRvci5jcHA=) | `99.60% <100.00%> (+0.05%)` | :arrow_up: |  
| [test/test\_message.cpp](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X21lc3NhZ2UuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [src/boost/locale/icu/collator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvY29sbGF0b3IuY3Bw) | `94.20% <94.11%> (+0.08%)` | :arrow_up: |  
| [src/boost/locale/win32/collate.cpp](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS93aW4zMi9jb2xsYXRlLmNwcA==) | `82.50% <85.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [941f853...1bd9b6c](https://app.codecov.io/gh/boostorg/locale/pull/216?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: artyom-beilis  
> Created_at: 2024-02-08 18:56:33 UTC  
> Url: https://github.com/boostorg/locale/pull/216#issuecomment-1934752435  

Small but important note: if the facet isn't derived from `std::collate` the `std::locale::operator()` can no longer perform its function. https://en.cppreference.com/w/cpp/locale/locale/operator()  
  
the std::locale can be used as replacement for `std::less` for string comparison.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2024-02-08 19:01:00 UTC  
> Url: https://github.com/boostorg/locale/pull/216#issuecomment-1934758615  

Yes but we have 2 things here: `std::collate` implementation with "proper unicode support" and `boost::locale::collator` which is similar to `std::collate` but provides support for collation levels, used by e.g. `comparator`  
  
I split those 2 into 2 classes because not all backends implement `boost::locale::collator`. Where they do an adapter class is used deriving from `std::collate` to perform the function you mention.

---

## Comment 4

> Username: artyom-beilis  
> Created_at: 2024-02-08 19:09:51 UTC  
> Url: https://github.com/boostorg/locale/pull/216#issuecomment-1934771345  

Yes I understand that `boost::locale::collator` is much better. But the fact that the facet is derived from `std::collate` allows to use something that is provided by standard library (even lesser so):  
  
  
    // standard  
    std::locale loc = std::locale("ru_RU.UTF-8");   
    // boost  
    boost::locale::generator gen;  
    std::locale loc = gen("ru_RU.UTF-8");   
    // same for both variants  
    std::sort(v.begin(), v.end(), loc);  
  
Something that was direct upgrade path from existing locales (BTW collation is something that actually works with standard locale on Linux quite ok)  
  
I don't know how many users will actually be affected but this is something to think about - if it is worth breaking the code.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2024-02-09 08:05:32 UTC  
> Url: https://github.com/boostorg/locale/pull/216#issuecomment-1935488316  

> But the fact that the facet is derived from `std::collate` allows to use something that is provided by standard library (even lesser so):  
  
Being derived from `std::collate` implementing additional virtual methods leads to actual, subtile bugs -> See #215     
Hence the change to make this safer  
  
> ```  
> // standard  
> std::locale loc = std::locale("ru_RU.UTF-8");   
> // boost  
> boost::locale::generator gen;  
> std::locale loc = gen("ru_RU.UTF-8");   
> // same for both variants  
> std::sort(v.begin(), v.end(), loc);  
> ```  
  
This still works as mentioned before. See especially https://github.com/boostorg/locale/pull/216/files#diff-9061a93b241603967564b57e006fdb1956988bbc54d82af2d5d3228e99634914  
  
> Something that was direct upgrade path from existing locales (BTW collation is something that actually works with standard locale on Linux quite ok)  
  
What exactly "works with standard locale on Linux quite ok"? Is that affected by this PR anyhow?  
  
> I don't know how many users will actually be affected but this is something to think about - if it is worth breaking the code.  
  
I don't think this affects many users. The only ones where code will be broken is if they assume `boost::locale::collator` derives from `std::collator` which will now fail to compile.      
Other code where there previously was a type confusion will now correctly throw a `std::bad_cast` as expected.     
All other code should continue to work as before as confirmed by the `test_collate` and `test_winapi_collate` tests

---
