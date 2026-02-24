# #38 - Remove hard error about C++11 requirement [Closed]

> Username: Lastique  
> Created at: 2023-08-13 17:31:55 UTC  
> Updated at: 2023-08-25 08:42:09 UTC  
> Closed at: 2023-08-25 08:41:54 UTC  
> Url: https://github.com/boostorg/type_index/issues/38  

Currently, `type_index.hpp` emits a [hard error](https://github.com/boostorg/type_index/blob/62a3c9e89fdc88b909f63c32c68fce36297a2d7e/include/boost/type_index.hpp#L36) if one of the C++11 feature test macros indicate a missing feature. Please remove this error and let the compilation fail naturally on unsupported compilers.  
  
The feature checks in that list are excessively strict. For example, `BOOST_NO_CXX11_HDR_TYPE_TRAITS` requires a fully functional `<type_traits>` header, which wasn't present until gcc 5.1. However, `<type_traits>` with an incomplete list of type traits appeared much earlier, I think since gcc 4.4, and that incomplete header may be sufficient for Boost.TypeIndex needs.  
  
Side note: `<typeindex>` appeared in gcc 4.6, but Boost.TypeIndex requires gcc 5. It would be logical if Boost.TypeIndex supported at least the same set of compilers that have native support for `type_index`, and preferably if it supported even more compilers. Otherwise there's little reason to use Boost.TypeIndex.

---

## Comment 1

> Username: apolukhin  
> Created at: 2023-08-25 08:42:08 UTC  
> Url: https://github.com/boostorg/type_index/issues/38#issuecomment-1692996277  

Good point!  
  
Many thanks for the report!
