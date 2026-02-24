# #581 Port TuttleOFX extension : pattern [Open]

> Username: meshtag  
> Created at: 2021-03-17 07:41:52 UTC  
> Updated at: 2021-04-15 11:30:00 UTC  
> Url: https://github.com/boostorg/gil/pull/581  

### Description  
  
This pull request intends to port ```pattern``` extension from [TuttleOFX](https://github.com/tuttleofx/TuttleOFX/tree/develop/libraries/boostHack/boost/gil/extension/toolbox) to Gil as mentioned in #449 .  
  
I have tried not to change anything significant in actual extension code. However, I have reformatted test cases completely in order to align them with current practices followed by Gil.  
  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-03-17 08:37:51 UTC  
> Updated_at: 2021-04-15 11:30:00 UTC  
> Url: https://github.com/boostorg/gil/pull/581#issuecomment-800901172  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/581?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#581](https://codecov.io/gh/boostorg/gil/pull/581?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1284963) into [develop](https://codecov.io/gh/boostorg/gil/commit/bbdce36a1f655ebf8332b775790673b197c8f1de?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (bbdce36) will **increase** coverage by `0.25%`.  
> The diff coverage is `92.85%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #581      +/-   ##  
===========================================  
+ Coverage    78.72%   78.98%   +0.25%       
===========================================  
  Files          118      119       +1       
  Lines         5034     5048      +14       
===========================================  
+ Hits          3963     3987      +24       
+ Misses        1071     1061      -10       
```

---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2021-04-09 19:56:17 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/581#pullrequestreview-632740268  

📁 include/boost/gil/extension/toolbox/metafunctions/pattern.hpp

```diff
   4 |+ 
   5 |+ #ifndef _pattern_hpp_
   6 |+ #define _pattern_hpp_
```

> Username: mloskot  
> Created_at: 2021-04-09 19:49:33 UTC  
> Updated_at: 2021-04-15 09:49:26 UTC  
> Url: https://github.com/boostorg/gil/pull/581#discussion_r610870437  

These header guards need to be changed, see https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines

---

📁 include/boost/gil/extension/toolbox/metafunctions/pattern.hpp

```diff
  12 |+ /// \brief Repeatedly copies smaller image view passed through the struct constructor in subimage
  13 |+ /// views of the larger image view passed through overloaded '()' operator.
  14 |+ template <typename view_t>
```

> Username: mloskot  
> Created_at: 2021-04-09 19:54:18 UTC  
> Updated_at: 2021-04-15 09:49:26 UTC  
> Url: https://github.com/boostorg/gil/pull/581#discussion_r610872648  

Could you rename template parameters to upper case, `View` ?

---

📁 include/boost/gil/extension/toolbox/metafunctions/pattern.hpp

```diff
  23 |+     void operator()(view_t& view)
  24 |+     {
  25 |+         using namespace boost::gil;
```

> Username: mloskot  
> Created_at: 2021-04-09 19:54:40 UTC  
> Updated_at: 2021-04-15 09:49:26 UTC  
> Url: https://github.com/boostorg/gil/pull/581#discussion_r610872797  

I doubt this `using` import is necessary

---

📁 include/boost/gil/extension/toolbox/metafunctions/pattern.hpp

```diff
  25 |+         using namespace boost::gil;
  26 |+ 
  27 |+         std::size_t h = v2.height(), w = v2.width();
```

> Username: mloskot  
> Created_at: 2021-04-09 19:55:26 UTC  
> Updated_at: 2021-04-15 09:49:26 UTC  
> Url: https://github.com/boostorg/gil/pull/581#discussion_r610873165  

Some original code review: if not modified, make it const `std::size_t const h`


📁 test/extension/toolbox/CMakeLists.txt

```diff
  27 |-   subchroma_image)
  27 |+   subchroma_image
  28 |+   pattern)
```

> Username: mloskot  
> Created_at: 2021-04-09 19:55:56 UTC  
> Updated_at: 2021-04-15 09:49:26 UTC  
> Url: https://github.com/boostorg/gil/pull/581#discussion_r610873419  

Could you sort it alphabetically?


---
