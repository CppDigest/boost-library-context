# #45 - boost::typeindex::ctti_type_index::type_id<T>().name() produces unexpected results [Closed]

> Username: jcfromsiberia  
> Created at: 2025-12-12 07:32:06 UTC  
> Updated at: 2025-12-30 21:10:57 UTC  
> Closed at: 2025-12-30 21:10:57 UTC  
> Url: https://github.com/boostorg/type_index/issues/45  

https://godbolt.org/z/vdzns1W8P  
Boost 1.89.0  
  
For clang 21.1 and gcc 15.2 the expected name got suffixed with `]` whereas for MSVC 19.44 (alas compiler explorer could not find the boost header, compiled locally) I got an even more obscure name: `struct Foo>::n(void) noexcept`.  
  
Couldn't find any other `constexpr` typename alternatives.

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-12-26 19:48:45 UTC  
> Url: https://github.com/boostorg/type_index/issues/45#issuecomment-3693291321  

To get the pretty name you have to use `boost::typeindex::ctti_type_index::type_id<int>().pretty_name()`. Unfortunately it is not `constexpr`.  
  
However, the code for `name()` is quite old. I'm quite sure that it could be improved and it is possible to cut-of the trailing junk. Will give it a try soon
