# #27 Fix some misc issues [Merged]

> Username: Flast  
> Created at: 2014-10-28 11:01:40 UTC  
> Updated at: 2014-10-29 04:21:40 UTC  
> Merged at: 2014-10-28 23:22:39 UTC  
> Closed at: 2014-10-28 23:22:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/27  

### Summaries of each commit  
  
ae23ffb **Fix include guard mismatch**  
- `BOOST_FUSION_REPETITIVE_VIEW_ITERATOR_HPP_INCLUDED` v.s. `BOOST_FUSION_REPETITIVE_VIEW_HPP_ITERATOR_INCLUDED`  
  - This issue also affects master branch. I'll open hotfix pull-request if you want.  
  
90206f5 **Fix result type mismatch**  
- The variadic meta-function `result_of::make_deque<>::type` is inconsistent with the variadic function `make_deque` returning type: `as_fusion_element`-ized type returned actually.  
  
179e36f **Do not expand BOOST_NO_CXX11_RVALUE_REFERENCES while preprocessing.**  
- To protect unexpected expanding in some env.  
  - In my env, Wave should behave GCC(system native compiler) to process some include. Then Boost.Config will define `BOOST_NO_CXX11_RVALUE_REFERENCES` and Wave expands it to empty.  
  
8dc502d **Remove dup of internal macro**  
- `BOOST_FUSION_AS_FUSION_ELEMENT` is defined twice.  
  
c6cb258 **Fix doc typo and describe about c++11 make_map.**  
- `C++11` should be `C++03`.  
- For consistency with make_deque.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-10-28 23:22:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/27#issuecomment-60849201  

Again, very nice! Thank you!

---
