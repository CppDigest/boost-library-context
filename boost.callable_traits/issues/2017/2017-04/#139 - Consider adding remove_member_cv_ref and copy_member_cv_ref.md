# #139 - Consider adding remove_member_cv_ref and copy_member_cv_ref [Open]

> Username: ldionne  
> Created at: 2017-04-17 04:07:40 UTC  
> Updated at: 2018-07-30 14:46:14 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/139  

Taken from https://lists.boost.org/Archives/boost/2017/04/234374.php:  
  
>> In "member qualifier features", I'm missing `remove_member_cv_ref` (obvious semantics) and `copy_member_cv_ref` (copies cv+ref from one member pointer to another.)   
  
and your reply in https://lists.boost.org/Archives/boost/2017/04/234446.php:  
  
> I'm slightly hesitant to add `remove_member_cv_ref` (`remove_member_qualifiers`?), because it doesn't quite mirror `<type_traits>`. Would this also remove `noexcept`/`transaction_safe`? I'm conflicted about `copy_member_cv_ref`, because I'm not sure whether it would be more useful to copy them to a function type or value type.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-04-17 13:13:16 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/139#issuecomment-294483262  

I admit I don't have a use case for `copy_member_cv_ref`, so take this suggestion with a grain of salt. `copy_cv` is often useful for types (`T cv, U` -> `U cv`), but the analogy may not hold here.

---

## Comment 2

> Username: willwray  
> Created at: 2018-07-26 21:01:27 UTC  
> Updated at: 2018-07-30 14:46:14 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/139#issuecomment-408233939  

FYI I have a use case for `copy_member_qualifiers` including `noexcept` along with `cv`/`ref` (so the target then has to be a function type).  
  
My application is type reflection where each C++ compound has a reflected version (denoted here with a capital or capitalised acronym):  
  
1. `type` -> `Type`  
2. `type const` -> `CV<Type const>`  
3. `type&` -> `Ref<Type&>`  
4. `ret(args...) const && noexcept` -> `Fun<Ret(Args...) const && noexcept>`  
  
(2) needs a `copy_cv`, (3) needs a `copy_ref` and (4) needs this `copy_cvref_noexcept`  
  
Using the actual qualifiers in the reflected type saves inventing a scheme for encoding and decoding the qualifiers.  
  
This is just FYI, not a request to add this trait to callable_traits.
