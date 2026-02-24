# #211 - Cleanup static variables [Open]

> Username: zufuliu  
> Created at: 2024-05-12 08:03:19 UTC  
> Updated at: 2024-05-12 08:49:17 UTC  
> Url: https://github.com/boostorg/regex/issues/211  

1. `static const int hash_value_mask` can be changed to `static constexpr int hash_value_mask`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/basic_regex.hpp#L61  
  
2. `static` on trivial `w` and `s` variables can be removed:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/basic_regex_creator.hpp#L267-L268  
  
3. `static const std::uintmax_t one ` can be changed to `constexpr std::uintmax_t one`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/basic_regex_creator.hpp#L1470  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/basic_regex_creator.hpp#L1490  
  
4. `static const char incomplete_message[]` can be changed to `const char * const incomplete_message`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/basic_regex_parser.hpp#L852  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/basic_regex_parser.hpp#L1132  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/basic_regex_parser.hpp#L1338  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/basic_regex_parser.hpp#L1430  
  
5. `static const bool value` can be changed to `static constexpr bool value`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/iterator_category.hpp#L36  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/iterator_category.hpp#L42  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/iterator_category.hpp#L75  
  
6. `const bool is_random_access_iterator` can be changed to `constexpr bool is_random_access_iterator`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/iterator_category.hpp#L79  
these two lines can be removed after change line 75 to `constexpr`.  
  
7. `static const std::ptrdiff_t k` can be changed to `constexpr std::ptrdiff_t k`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/perl_matcher_common.hpp#L104  
  
8. `static const bool value` can be changed to `static constexpr bool value`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/regex_traits.hpp#L59  
  
9. big array `const char* messages[]` can be marked as `static`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/regex_traits_defaults.hpp#L53  
  
11. big array `char_syntax[]` can be marked as `const`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/regex_traits_defaults.hpp#L158  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/regex_traits_defaults.hpp#L353  
  
12. big array `combining_ranges[]` can be marked as `static`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/regex_traits_defaults.hpp#L545  
  
13. `static const` for trivial variables can be changed to `constexpr`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/regex_traits_defaults.hpp#L897-L902  
  
14. `static const char_class_type` can be change to `static constexpr char_class_type`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/w32_regex_traits.hpp#L411-L415  
  
15. `static const string_type null_string;` can be changed to <del>`constexpr string_type null_string;`</del> `const string_type null_string;`:  
https://github.com/boostorg/regex/blob/cb559132939670aa45eb25fd867fce0be8b08837/include/boost/regex/v5/w32_regex_traits.hpp#L589

---

## Comment 1

> Username: zufuliu  
> Created at: 2024-05-12 08:05:35 UTC  
> Url: https://github.com/boostorg/regex/issues/211#issuecomment-2106158583  

With these changes, Notepad2 built by MSVC is 1KB~2KB smaller.
