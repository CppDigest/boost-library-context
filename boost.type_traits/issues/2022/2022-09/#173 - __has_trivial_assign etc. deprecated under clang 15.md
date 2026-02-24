# #173 - __has_trivial_assign etc. deprecated under clang 15 [Closed]

> Username: ecatmur  
> Created at: 2022-09-06 18:57:27 UTC  
> Updated at: 2022-09-13 11:08:30 UTC  
> Closed at: 2022-09-13 11:08:30 UTC  
> Url: https://github.com/boostorg/type_traits/issues/173  

https://releases.llvm.org/15.0.0/tools/clang/docs/ReleaseNotes.html#non-comprehensive-list-of-changes-in-this-release  
  
> Some type-trait builtins, such as `__has_trivial_assign`, have been documented as deprecated for a while because their semantics don’t mix well with post-C++11 type-traits. Clang now emits deprecation warnings for them under the flag `-Wdeprecated-builtins`.  
  
Should be OK to replace with `__is_trivially_assignable` etc. (with some adjustments). Will post a patch.

---

## Comment 1

> Username: ecatmur  
> Created at: 2022-09-06 18:58:17 UTC  
> Url: https://github.com/boostorg/type_traits/issues/173#issuecomment-1238535911  

example:  
```  
boost/type_traits/has_trivial_destructor.hpp:30:86: error: builtin __has_trivial_destructor is deprecated; use __is_trivially_destructible instead [-Werror,-Wdeprecated-builtins]  
template <typename T> struct has_trivial_destructor : public integral_constant<bool, BOOST_HAS_TRIVIAL_DESTRUCTOR(T)>{};  
                                                                                     ^  
boost/type_traits/intrinsics.hpp:196:47: note: expanded from macro 'BOOST_HAS_TRIVIAL_DESTRUCTOR'  
#     define BOOST_HAS_TRIVIAL_DESTRUCTOR(T) (__has_trivial_destructor(T)  && is_destructible<T>::value)  
                                              ^  
```
