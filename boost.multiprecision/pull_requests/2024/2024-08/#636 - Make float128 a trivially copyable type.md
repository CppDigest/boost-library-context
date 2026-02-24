# #636 Make float128 a trivially copyable type [Merged]

> Username: nmnobre  
> Created at: 2024-08-02 15:42:41 UTC  
> Updated at: 2024-08-06 17:39:09 UTC  
> Merged at: 2024-08-06 17:36:22 UTC  
> Closed at: 2024-08-06 17:36:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/636  

Closes #635.  
  
Opted to go for trivially copyable and not fully trivial as otherwise we'd have to make the `constexpr float128_backend() noexcept` constructor trivial but I expect we'd like to keep zero initialisation for the data member `m_value` we're wrapping even when using default initialisation. In any case, I never needed the type to be trivial, only trivially copyable, so I'm happy if you are?  
  
Code snippet to print type traits and limits: https://godbolt.org/z/ddsj16M7W. I'm not sure we need everything in there, I just copied the original snippet and added a trivially copyable check, but feel free to edit it to your liking and we can then amend the website. :)

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-08-02 16:16:19 UTC  
> Updated_at: 2024-08-02 16:59:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/636#issuecomment-2265732124  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/636?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`0f6a276`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/0f6a276756e2fb3b9226000a3b24f788787e2474?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`320d8b7`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/320d8b7a9bb103d57b2950f6f7df2544d8547068?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/636/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/636?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #636     +/-   ##  
=========================================  
- Coverage     94.1%   94.1%   -0.0%       
=========================================  
  Files          276     276               
  Lines        26846   26839      -7       
=========================================  
- Hits         25240   25232      -8       
- Misses        1606    1607      +1       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/multiprecision/pull/636?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/number.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/636?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fnumber.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9udW1iZXIuaHBw) | `96.5% <100.0%> (-<0.1%)` | :arrow_down: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/636/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/636?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/636?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0f6a276...320d8b7](https://app.codecov.io/gh/boostorg/multiprecision/pull/636?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-08-06 17:39:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/636#issuecomment-2271809798  

Thanks for this. I also added a trivial test: https://github.com/boostorg/multiprecision/commit/c2b11c1e9170686f997b158d368b3c83e3f572a4, and update the code snippet from your original question: https://github.com/boostorg/multiprecision/commit/b16cff21e3bcd5d7e30538578913fb2373c1720a

---
