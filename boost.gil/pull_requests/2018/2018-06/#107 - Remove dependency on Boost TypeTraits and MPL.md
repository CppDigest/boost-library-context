# #107 Remove dependency on Boost TypeTraits and MPL [Closed]

> Username: mloskot  
> Created at: 2018-06-25 07:57:47 UTC  
> Updated at: 2020-07-24 13:39:24 UTC  
> Closed at: 2018-10-29 21:41:42 UTC  
> Url: https://github.com/boostorg/gil/pull/107  

(NOTE: _Assigned to self - if accepted, I'd like to merge myself. Thanks._)  
  
### Description: what does this pull request do?  
  
Replace uses of Boost.TypeTraits with C++11 `<type_traits>` features.  
Replace custom Boost.TypeTraits specializations injected into boost namespace with equivalents injected `std` namespace.  
Replace Boost.MPL type`bool_`, constants `false_` and `true_` with C++11 `std::false_type` and `std::true_type`.  
Define `std::bool_constant` for compilers w/o C++17 features.  
  
### Tasklist  
  
- [ ] Replace indirect uses of `boost::mpl::bool_` returned from MPL metafunctions (eg. `boost::mpl::contains`) - **remember** `std::is_same(mpl::bool_, std::bool_constant)` is NOT  
- [ ] Review  
- [ ] Adjust for comments  
- [ ] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-08-27 07:09:46 UTC  
> Url: https://github.com/boostorg/gil/pull/107#issuecomment-416135496  

ATM, this PR is broken, see [Recent develop vs master shuffles broke existing pull requests](https://lists.boost.org/boost-gil/2018/08/0075.php)

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-10-29 21:41:42 UTC  
> Url: https://github.com/boostorg/gil/pull/107#issuecomment-434091479  

I'm closing this one  as dropped. I'm planning to take a slightly different route based on number of smaller steps, with local changes.

---
