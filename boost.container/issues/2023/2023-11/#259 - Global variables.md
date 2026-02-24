# #259 - Global variables [Closed]

> Username: gast128  
> Created at: 2023-11-27 13:05:11 UTC  
> Updated at: 2024-06-16 21:33:51 UTC  
> Closed at: 2024-06-16 21:33:50 UTC  
> Url: https://github.com/boostorg/container/issues/259  

Hello all,  
  
tried to debug our startup code and get a lot of hits in initialization code of Boost.Container due to some global static variables:  
  
- static const vector_uninitialized_size_t vector_uninitialized_size = vector_uninitialized_size_t();  
- static const ordered_range_t ordered_range = ordered_range_t();  
- static const ordered_unique_range_t ordered_unique_range = ordered_unique_range_t();  
- static const default_init_t default_init = default_init_t();  
- static const value_init_t value_init = value_init_t();  
- static piecewise_construct_t piecewise_construct = BOOST_CONTAINER_DOC1ST(unspecified, *std_piecewise_construct_holder<>::dummy);  
  
Can these be turned into inline constexpr for C++17 and higher; e.g.:  
  
- BOOST_INLINE_CONSTEXPR vector_uninitialized_size_t vector_uninitialized_size = vector_uninitialized_size_t();  
- BOOST_INLINE_CONSTEXPR ordered_range_t ordered_range = ordered_range_t();  
- etc.  
  
I think BOOST_INLINE_CONSTEXPR expands to 'inline constexpr' when available and otherwise to 'const'. It may miss the 'static const' case though. Perhaps there is a Boost guideline.  
  
Using Boost 1.79 but it seems not changed since then.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-06-16 21:33:50 UTC  
> Url: https://github.com/boostorg/container/issues/259#issuecomment-2171894877  

Thanks for the report. The fix was implemented in commit:  
  
https://github.com/boostorg/container/commit/a4c4c3b3191ece1396e0a863e63634600b58a44a
