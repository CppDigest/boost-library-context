# #294 [numeric] Add gil::transform multiplication workaround for MSVC=1900 [Closed]

> Username: mloskot  
> Created at: 2019-04-25 19:17:51 UTC  
> Updated at: 2019-05-02 15:52:14 UTC  
> Closed at: 2019-04-29 18:59:54 UTC  
> Url: https://github.com/boostorg/gil/pull/294  

UPDATE: This PR has been closed as not merged and replaced by PR #295  
  
------  
  
For some reason, MSVC, for this `point<F2> * matrix3x2<F>` instead of calling  
  
```  
template <typename T, typename F>  
point<F> operator*(point<T> const& p, matrix3x2<F> const& m)  
```  
calls  
  
```  
template <typename T, typename M>  
auto operator*(point<T> const& p, M m)  
```  
  
There is no ambiguity reported by any other compiler/version (~20) that is currently CI-tested.  
  
### References  
  
- Fixes #289  
- Test added in #293  
  
### Tasklist  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2019-04-25 19:22:54 UTC  
> Url: https://github.com/boostorg/gil/pull/294#issuecomment-486805343  

Hmm, perhaps the  
```  
template <typename T, typename M>  
auto operator*(point<T> const& p, M m)  
```  
definition should be protected to constrain what types `M` can take ? (The usual `std::enable_if<>` logic...) That would at least solve the MSVC issue you mention.

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-04-25 19:48:53 UTC  
> Url: https://github.com/boostorg/gil/pull/294#issuecomment-486813549  

@stefanseefeld good idea. Please, have a look at #295

---
