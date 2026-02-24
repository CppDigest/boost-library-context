# #18 Support std::string_view & boost::string_view [Open]

> Username: poyenc  
> Created at: 2020-08-16 15:48:46 UTC  
> Updated at: 2022-04-27 22:21:04 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/18  

### Description  
Support both `std::string_view` and `boost::string_view` as tokenizer's string type argument for following separators:  
  
1. `offset_separator`  
2. `char_separator`  
3. `char_delimiters_separator`  
  
Due to the `escaped_list_separator`'s transformation semantics, we cannot simply use `std::string_view` as return token type. But in the future, I think we can let users to decide the return token type and input type separately instead of bind them together, to support `escaped_list_separator` in some conditions, e.g. tokenize `std::string_view` to `std::string`(s).  
  
### Changes  
  
1. Remove useless helper type `assign_or_plus_equal` which does nothing special.  
2. Add named function templates `assign()`/`append()`/`clear()` for token object to replace overload operator usages, and provide customization points to users (find candidates via *ADL*).  
3. Remove repeated string reading logic, improve time complexity from *O(2n)* down to *O(n)*.  
  
### Note  
This pull request is based on #17, please merge it first.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-08-16 18:20:12 UTC  
> Updated_at: 2020-08-17 21:08:32 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/18#issuecomment-674559766  

# [Codecov](https://codecov.io/gh/boostorg/tokenizer/pull/18?src=pr&el=h1) Report  
> Merging [#18](https://codecov.io/gh/boostorg/tokenizer/pull/18?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/tokenizer/commit/1c8a8bfd3a8fc2071458884da23ef5ac3a778a0c&el=desc) will **increase** coverage by `17.72%`.  
> The diff coverage is `87.80%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/tokenizer/pull/18/graphs/tree.svg?width=650&height=150&src=pr&token=sakwglU1PC)](https://codecov.io/gh/boostorg/tokenizer/pull/18?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff              @@  
##           develop      #18       +/-   ##  
============================================  
+ Coverage    70.95%   88.67%   +17.72%       
============================================  
  Files            3        3                 
  Lines          210      212        +2       
  Branches        74       64       -10       
============================================  
+ Hits           149      188       +39       
+ Misses          18        0       -18       
+ Partials        43       24       -19       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/tokenizer/pull/18?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/token\_functions.hpp](https://codecov.io/gh/boostorg/tokenizer/pull/18/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC90b2tlbl9mdW5jdGlvbnMuaHBw) | `88.88% <87.50%> (+17.70%)` | :arrow_up: |  
| [include/boost/token\_iterator.hpp](https://codecov.io/gh/boostorg/tokenizer/pull/18/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC90b2tlbl9pdGVyYXRvci5ocHA=) | `88.46% <100.00%> (+18.09%)` | :arrow_up: |  
| [include/boost/tokenizer.hpp](https://codecov.io/gh/boostorg/tokenizer/pull/18/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC90b2tlbml6ZXIuaHBw) | `83.33% <0.00%> (+16.66%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/tokenizer/pull/18?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/tokenizer/pull/18?src=pr&el=footer). Last update [1c8a8bf...6a3c01b](https://codecov.io/gh/boostorg/tokenizer/pull/18?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: poyenc  
> Created_at: 2020-08-16 18:27:46 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/18#issuecomment-674560545  

need to figure out why we have coverage drop

---

## Review 3 [Changes requested]

> Username: jeking3  
> Created_at: 2022-04-27 22:21:04 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/tokenizer/pull/18#pullrequestreview-955596752  

Please do not check in a .gitignore file, it is not needed here (you can use user profile based ignores if necessary).  
  
Please first get #17 committed then rebase this on top of that.  
  
Thanks!

---
