# #78 - C++20 breaks optional<std::filesystem::path> [Closed]

> Username: CaseyCarter  
> Created at: 2019-12-19 01:51:44 UTC  
> Updated at: 2019-12-20 07:39:56 UTC  
> Closed at: 2019-12-19 23:31:25 UTC  
> Url: https://github.com/boostorg/optional/issues/78  

Per detailed discussion [on Developer Community](https://developercommunity.visualstudio.com/content/problem/856862/compile-error-when-assigning-to-boostoptional-from.html?childToView=861780#comment-861780), determining if `optional<std::filesystem::path>` is copy assignable results in constraint recursion in C++20.  
  
The fix is to avoid instantiating [the `is_constructible` check in `is_convertible_to_T_or_factory`](https://github.com/boostorg/optional/blob/develop/include/boost/optional/optional.hpp#L796) when [the `is_optional_related<U>` check in `is_optional_val_init_candidate`](https://github.com/boostorg/optional/blob/develop/include/boost/optional/optional.hpp#L818) yields `true`.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2019-12-20 07:39:56 UTC  
> Url: https://github.com/boostorg/optional/issues/78#issuecomment-567825714  

Thank you for the report and fix.
