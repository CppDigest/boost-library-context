# #177 - Specification of function qualifier traits for function pointer and reference types [Closed]

> Username: willwray  
> Created at: 2018-07-30 16:22:06 UTC  
> Updated at: 2018-12-17 20:51:40 UTC  
> Closed at: 2018-12-17 20:51:27 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/177  

The `callable_traits` specifications of `is_QUAL_member` and `is_noexcept` traits seem inconsistent with similar `std` traits which do not see through reference or pointer (for QUAL in const, volatile, cv, reference, lvalue_reference, rvalue_reference).  
  
For instance:<br>`std::is_const_v<T&>` -> `false` regardless of `T`<br>`ct::is_noexcept_v<T&>` -> ? - dependent on `T`  
  
The particular case of `is_noexcept` is vexing. Should it be spelt `is_noexcept_member` for consistency with the other `is_QUAL_member` traits? The simple spelling `is_noexcept` might be taken as a static version of the `noexcept()` operator, something like:<br>`template <typename F> inline constexpr bool is_noexcept = noexcept(std::declval<F>()());`<br>(note that such use of noexcept operator is not viable in general - it assumes F is callable with no args, falling down when args are needed, but expanding `Args...` means also checking for all 48 abominable combos by which time the noexcept qualifier is known anyhow).  
  
These callable_traits smell like they are trying to do too much:  
>`is_QUAL_member<T>::value` / `is_noexcept<T>::value` is true when either:  
T is a function type, function pointer type, function reference type, or member function pointer type where the function has a `QUAL` / `noexcept` specifier  
T is a function object with a non-overloaded operator(), where the operator() has a `QUAL` / `noexcept` specifier  
  
(That last line is very specific - checking for no-args function object.)  
  
In the Input/Output examples:  
>`is_noexcept<int(* const &)() noexcept>` -> `true`  
  
In this case the trait is stripping off `&` and `* const` to expose the function signature and its qualifiers. The documented description of the behaviour above doesn't mention this recursive stripping.  
  
More vexing - should `is_noexcept` default to `true` for non-function types? Is a non-function type noexcept or not? (The `is_noexcept_member` spelling would make this more clear.)  
  
I think that there's value in exposing the underlying checks themselves without the recursive pointer/reference unwrapping.

---

## Comment 1

> Username: badair  
> Created at: 2018-10-18 16:55:24 UTC  
> Updated at: 2018-10-18 16:55:43 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/177#issuecomment-431084230  

> The callable_traits specifications of is_QUAL_member and is_noexcept traits seem inconsistent with similar std traits which do not see through reference or pointer (for QUAL in const, volatile, cv, reference, lvalue_reference, rvalue_reference).  
  
That's fair and accurate. The feature to see through top level CV/ref was requested during the Boost review. I went back and forth about this, ultimately deciding to deviate from the standard library's conventions here. I decided to do this because CallableTraits features would commonly used with perfectly forwarded template parameters, where ref-ness and CV-ness would always need to be manually discarded before passing to a CallableTraits metafunction or trait template. For better or worse, this decision has been made and I can't change without breaking user code now that the library has been released.  
  
> The particular case of is_noexcept is vexing. Should it be spelt is_noexcept_member for consistency with the other is_QUAL_member traits? The simple spelling is_noexcept might be taken as a static version of the noexcept() operator  
  
I used the `_member` suffix to distinguish from the normal metafunctions like `std::is_const` (for `T const` where `T` is not a function type), and since there is no `T noexcept` case where `T` is not a function type I did not consider the `_member` suffix here. You make a good point, and I wish I had received this criticism before the library was released.  
  
> These callable_traits smell like they are trying to do too much: ... (That last line is very specific - checking for no-args function object.)  
  
It's not checking for no-args -- basically, the last line there says that the feature works with non-generic lambdas but not generic lambdas (I should make this clearer).  
  
> The documented description of the behaviour above doesn't mention this recursive stripping.  
  
Good point, I should add a bullet point for this. I intended the input/output table to be sufficient documentation for this functionality, but I suppose it is not enough.  
  
> More vexing - should is_noexcept default to true for non-function types? Is a non-function type noexcept or not? (The is_noexcept_member spelling would make this more clear.)  
  
I agree that `is_noexcept_member` would have made the default for non-function types clearer, especially considering that a pointer-to-member-data can be dereferenced without throwing.  
  
> I think that there's value in exposing the underlying checks themselves without the recursive pointer/reference unwrapping.  
  
Can you think of a case where exposing this behavior would be useful? I couldn't think of a case where the recursive unwrapping was problematic, which factored into my descision to unwrap them in the first place.  
  
Thanks @willwray for all the excellent feedback!

---

## Comment 2

> Username: willwray  
> Created at: 2018-10-18 19:04:16 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/177#issuecomment-431125476  

Thanks for the detailed responses with explanations. After submitting this issue I did eventually read through the Boost review, gaining a better understanding of CallableTraits, its use cases and design goals.  
  
It turned out that my needs were simpler than those that CallableTraits provides for; I needed traits for C++ function types only, not for more general callable types. In particular, I didn't need any generic handling of callables or any stripping of reference and/or pointers.  
  
> Can you think of a case where exposing this behavior would be useful? I couldn't think of a case where the recursive unwrapping was problematic  
  
In my case (see my issue 139 [comment](https://github.com/boostorg/callable_traits/issues/139#issuecomment-408233939)) unwrapping is not problematic, just unnecessary because reference and pointer will already be stripped as part of a higher level recursive 'decompounding' of types.  
  
Writing traits based on the full '48 specializations' seemed like a chore so I looked for a library solution and only found CallableTraits. In the end I wrote a `function_traits` lib; a single header of 300 LoC (before macro expansion - using X-macros rather than the header-inclusion method used in CallableTraits). Naming was the hardest part (I chose to also expand the names via X-macro which enforced a uniform naming scheme).   
  
One design choice in CallableTraits still puzzles me; the choice to provide fine-grained headers, one per trait. Fine-grain headers are unlikely to improve compile time here (I didn't measure...) as each trait has to pull in the full 48 specializations anyway. Similarly for dependency - the traits are all dependent on a common implementation class. Arguments for fine-grain headers might be code clarity and organisation, modularity or because Boost...  
  
I learned a lot from studying CallableTraits. Thanks for your work.  
  
Feel free to close this issue; no changes needed.

---

## Comment 3

> Username: badair  
> Created at: 2018-12-17 20:51:27 UTC  
> Updated at: 2018-12-17 20:51:40 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/177#issuecomment-447994326  

> One design choice in CallableTraits still puzzles me; the choice to provide fine-grained headers  
  
@willwray I did this because I would like to eventually consider refactoring CallableTraits so that each trait has its own dedicated specializations stamped out. The current design favors memoization of the specialized template instantiations. However, if a user needs only a small subset of the features in CallableTraits (as most users probably will), their compiler shouldn't have to parse the code for all the other features as well.  
  
Thanks again for your feedback on this library, and I'm sorry it didn't quite suit your needs.
