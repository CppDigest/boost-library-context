# #194 core: add an implementation based on C++26 destructuring into a pack [Closed]

> Username: jcelerier  
> Created at: 2025-03-02 15:48:44 UTC  
> Updated at: 2025-09-07 17:27:46 UTC  
> Closed at: 2025-09-07 17:27:45 UTC  
> Url: https://github.com/boostorg/pfr/pull/194  

Tested with clang-21 HEAD which supports destructuring into a pack and pack indexing <3  
  
I don't know how to use b2 / jam so I added some of the tests with CMake, however that excludes  
  
- the tests that use the BOOST_PFR_RUN_TEST_ON macro feature  
- test/core/run/huge_count.cpp which eats more ram than I have and gets oom-killed ?  
- all the "compile-fail" tests  
  
```  
[0/1] Running tests...  
Test project /home/jcelerier/projets/oss/boost/pfr/build-clang21  
      Start  1: _home_jcelerier_projets_oss_boost_pfr_test_config_print_config_cpp  
 1/53 Test  #1: _home_jcelerier_projets_oss_boost_pfr_test_config_print_config_cpp .............................................   Passed    0.00 sec  
      Start  2: _home_jcelerier_projets_oss_boost_pfr_test_core_can_be_as_fallback_in_the_fusion_cpp  
 2/53 Test  #2: _home_jcelerier_projets_oss_boost_pfr_test_core_can_be_as_fallback_in_the_fusion_cpp ...........................   Passed    0.00 sec  
      Start  3: _home_jcelerier_projets_oss_boost_pfr_test_core_fields_count_on_incomplete_type_cpp  
 3/53 Test  #3: _home_jcelerier_projets_oss_boost_pfr_test_core_fields_count_on_incomplete_type_cpp ............................   Passed    0.00 sec  
      Start  4: _home_jcelerier_projets_oss_boost_pfr_test_core_loophole_detection_cpp  
 4/53 Test  #4: _home_jcelerier_projets_oss_boost_pfr_test_core_loophole_detection_cpp .........................................   Passed    0.00 sec  
      Start  5: _home_jcelerier_projets_oss_boost_pfr_test_core_offset_based_getter_cpp  
 5/53 Test  #5: _home_jcelerier_projets_oss_boost_pfr_test_core_offset_based_getter_cpp ........................................   Passed    0.00 sec  
      Start  6: _home_jcelerier_projets_oss_boost_pfr_test_core_run_bitfields_count_cpp  
 6/53 Test  #6: _home_jcelerier_projets_oss_boost_pfr_test_core_run_bitfields_count_cpp ........................................   Passed    0.00 sec  
      Start  7: _home_jcelerier_projets_oss_boost_pfr_test_core_run_constexpr_ops_cpp  
 7/53 Test  #7: _home_jcelerier_projets_oss_boost_pfr_test_core_run_constexpr_ops_cpp ..........................................   Passed    0.00 sec  
      Start  8: _home_jcelerier_projets_oss_boost_pfr_test_core_run_core17_generated_cpp  
 8/53 Test  #8: _home_jcelerier_projets_oss_boost_pfr_test_core_run_core17_generated_cpp .......................................   Passed    0.00 sec  
      Start  9: _home_jcelerier_projets_oss_boost_pfr_test_core_run_destructuring_tie_cpp  
 9/53 Test  #9: _home_jcelerier_projets_oss_boost_pfr_test_core_run_destructuring_tie_cpp ......................................   Passed    0.00 sec  
      Start 10: _home_jcelerier_projets_oss_boost_pfr_test_core_run_error_pfr_c1202_cpp  
10/53 Test #10: _home_jcelerier_projets_oss_boost_pfr_test_core_run_error_pfr_c1202_cpp ........................................   Passed    0.00 sec  
      Start 11: _home_jcelerier_projets_oss_boost_pfr_test_core_run_fields_count_on_const_cpp  
11/53 Test #11: _home_jcelerier_projets_oss_boost_pfr_test_core_run_fields_count_on_const_cpp ..................................   Passed    0.00 sec  
      Start 12: _home_jcelerier_projets_oss_boost_pfr_test_core_run_for_each_field_cpp  
12/53 Test #12: _home_jcelerier_projets_oss_boost_pfr_test_core_run_for_each_field_cpp .........................................   Passed    0.00 sec  
      Start 13: _home_jcelerier_projets_oss_boost_pfr_test_core_run_functions_for_cpp  
13/53 Test #13: _home_jcelerier_projets_oss_boost_pfr_test_core_run_functions_for_cpp ..........................................   Passed    0.00 sec  
      Start 14: _home_jcelerier_projets_oss_boost_pfr_test_core_run_get_by_type_cpp  
14/53 Test #14: _home_jcelerier_projets_oss_boost_pfr_test_core_run_get_by_type_cpp ............................................   Passed    0.00 sec  
      Start 15: _home_jcelerier_projets_oss_boost_pfr_test_core_run_get_const_field_cpp  
15/53 Test #15: _home_jcelerier_projets_oss_boost_pfr_test_core_run_get_const_field_cpp ........................................   Passed    0.00 sec  
      Start 16: _home_jcelerier_projets_oss_boost_pfr_test_core_run_get_non_default_constructible_cpp  
16/53 Test #16: _home_jcelerier_projets_oss_boost_pfr_test_core_run_get_non_default_constructible_cpp ..........................   Passed    0.00 sec  
      Start 17: _home_jcelerier_projets_oss_boost_pfr_test_core_run_get_rvalue_cpp  
17/53 Test #17: _home_jcelerier_projets_oss_boost_pfr_test_core_run_get_rvalue_cpp .............................................   Passed    0.00 sec  
      Start 18: _home_jcelerier_projets_oss_boost_pfr_test_core_run_huge_count_cpp  
18/53 Test #18: _home_jcelerier_projets_oss_boost_pfr_test_core_run_huge_count_cpp .............................................   Passed    0.00 sec  
      Start 19: _home_jcelerier_projets_oss_boost_pfr_test_core_run_is_implicitly_reflectable_cpp  
19/53 Test #19: _home_jcelerier_projets_oss_boost_pfr_test_core_run_is_implicitly_reflectable_cpp ..............................   Passed    0.00 sec  
      Start 20: _home_jcelerier_projets_oss_boost_pfr_test_core_run_is_reflectable_cpp  
20/53 Test #20: _home_jcelerier_projets_oss_boost_pfr_test_core_run_is_reflectable_cpp .........................................   Passed    0.00 sec  
      Start 21: _home_jcelerier_projets_oss_boost_pfr_test_core_run_issue30_cpp  
21/53 Test #21: _home_jcelerier_projets_oss_boost_pfr_test_core_run_issue30_cpp ................................................   Passed    0.00 sec  
      Start 22: _home_jcelerier_projets_oss_boost_pfr_test_core_run_issue33_cpp  
22/53 Test #22: _home_jcelerier_projets_oss_boost_pfr_test_core_run_issue33_cpp ................................................   Passed    0.00 sec  
      Start 23: _home_jcelerier_projets_oss_boost_pfr_test_core_run_many_fields_count_cpp  
23/53 Test #23: _home_jcelerier_projets_oss_boost_pfr_test_core_run_many_fields_count_cpp ......................................   Passed    0.00 sec  
      Start 24: _home_jcelerier_projets_oss_boost_pfr_test_core_run_motivating_example_cpp  
24/53 Test #24: _home_jcelerier_projets_oss_boost_pfr_test_core_run_motivating_example_cpp .....................................   Passed    0.00 sec  
      Start 25: _home_jcelerier_projets_oss_boost_pfr_test_core_run_motivating_example2_cpp  
25/53 Test #25: _home_jcelerier_projets_oss_boost_pfr_test_core_run_motivating_example2_cpp ....................................   Passed    0.00 sec  
      Start 26: _home_jcelerier_projets_oss_boost_pfr_test_core_run_non_copyable_but_movable_cpp  
26/53 Test #26: _home_jcelerier_projets_oss_boost_pfr_test_core_run_non_copyable_but_movable_cpp ...............................   Passed    0.00 sec  
      Start 27: _home_jcelerier_projets_oss_boost_pfr_test_core_run_non_dc_non_cop_but_mov_cpp  
27/53 Test #27: _home_jcelerier_projets_oss_boost_pfr_test_core_run_non_dc_non_cop_but_mov_cpp .................................   Passed    0.00 sec  
      Start 28: _home_jcelerier_projets_oss_boost_pfr_test_core_run_non_default_constructible_cpp  
28/53 Test #28: _home_jcelerier_projets_oss_boost_pfr_test_core_run_non_default_constructible_cpp ..............................   Passed    0.00 sec  
      Start 29: _home_jcelerier_projets_oss_boost_pfr_test_core_run_non_movable_cpp  
29/53 Test #29: _home_jcelerier_projets_oss_boost_pfr_test_core_run_non_movable_cpp ............................................   Passed    0.00 sec  
      Start 30: _home_jcelerier_projets_oss_boost_pfr_test_core_run_non_std_layout_cpp  
30/53 Test #30: _home_jcelerier_projets_oss_boost_pfr_test_core_run_non_std_layout_cpp .........................................   Passed    0.00 sec  
      Start 31: _home_jcelerier_projets_oss_boost_pfr_test_core_run_ops_cpp  
31/53 Test #31: _home_jcelerier_projets_oss_boost_pfr_test_core_run_ops_cpp ....................................................   Passed    0.00 sec  
      Start 32: _home_jcelerier_projets_oss_boost_pfr_test_core_run_optional_chrono_cpp  
32/53 Test #32: _home_jcelerier_projets_oss_boost_pfr_test_core_run_optional_chrono_cpp ........................................   Passed    0.00 sec  
      Start 33: _home_jcelerier_projets_oss_boost_pfr_test_core_run_optional_like_cpp  
33/53 Test #33: _home_jcelerier_projets_oss_boost_pfr_test_core_run_optional_like_cpp ..........................................   Passed    0.00 sec  
      Start 34: _home_jcelerier_projets_oss_boost_pfr_test_core_run_read_write_cpp  
34/53 Test #34: _home_jcelerier_projets_oss_boost_pfr_test_core_run_read_write_cpp .............................................   Passed    0.00 sec  
      Start 35: _home_jcelerier_projets_oss_boost_pfr_test_core_run_read_write_non_literal_cpp  
35/53 Test #35: _home_jcelerier_projets_oss_boost_pfr_test_core_run_read_write_non_literal_cpp .................................   Passed    0.00 sec  
      Start 36: _home_jcelerier_projets_oss_boost_pfr_test_core_run_std_interactions_cpp  
36/53 Test #36: _home_jcelerier_projets_oss_boost_pfr_test_core_run_std_interactions_cpp .......................................   Passed    0.00 sec  
      Start 37: _home_jcelerier_projets_oss_boost_pfr_test_core_run_structure_to_tuple_cpp  
37/53 Test #37: _home_jcelerier_projets_oss_boost_pfr_test_core_run_structure_to_tuple_cpp .....................................   Passed    0.00 sec  
      Start 38: _home_jcelerier_projets_oss_boost_pfr_test_core_run_template_constructor_cpp  
38/53 Test #38: _home_jcelerier_projets_oss_boost_pfr_test_core_run_template_constructor_cpp ...................................   Passed    0.00 sec  
      Start 39: _home_jcelerier_projets_oss_boost_pfr_test_core_run_template_forwarding_ref_cpp  
39/53 Test #39: _home_jcelerier_projets_oss_boost_pfr_test_core_run_template_forwarding_ref_cpp ................................   Passed    0.00 sec  
      Start 40: _home_jcelerier_projets_oss_boost_pfr_test_core_run_template_unconstrained_cpp  
40/53 Test #40: _home_jcelerier_projets_oss_boost_pfr_test_core_run_template_unconstrained_cpp .................................   Passed    0.00 sec  
      Start 41: _home_jcelerier_projets_oss_boost_pfr_test_core_run_tie_anonymous_cpp  
41/53 Test #41: _home_jcelerier_projets_oss_boost_pfr_test_core_run_tie_anonymous_cpp ..........................................   Passed    0.00 sec  
      Start 42: _home_jcelerier_projets_oss_boost_pfr_test_core_run_tie_anonymous_const_field_cpp  
42/53 Test #42: _home_jcelerier_projets_oss_boost_pfr_test_core_run_tie_anonymous_const_field_cpp ..............................   Passed    0.00 sec  
      Start 43: _home_jcelerier_projets_oss_boost_pfr_test_core_run_tuple_size_cpp  
43/53 Test #43: _home_jcelerier_projets_oss_boost_pfr_test_core_run_tuple_size_cpp .............................................   Passed    0.00 sec  
      Start 44: _home_jcelerier_projets_oss_boost_pfr_test_core_test_tuple_sizes_on_cpp  
44/53 Test #44: _home_jcelerier_projets_oss_boost_pfr_test_core_test_tuple_sizes_on_cpp ........................................   Passed    0.00 sec  
      Start 45: _home_jcelerier_projets_oss_boost_pfr_test_core_name_cxx20_address_of_non_static_member_tplarg_detection_cpp  
45/53 Test #45: _home_jcelerier_projets_oss_boost_pfr_test_core_name_cxx20_address_of_non_static_member_tplarg_detection_cpp ...   Passed    0.00 sec  
      Start 46: _home_jcelerier_projets_oss_boost_pfr_test_core_name_cxx20_nontype_tplarg_detection_cpp  
46/53 Test #46: _home_jcelerier_projets_oss_boost_pfr_test_core_name_cxx20_nontype_tplarg_detection_cpp ........................   Passed    0.00 sec  
      Start 47: _home_jcelerier_projets_oss_boost_pfr_test_core_name_print_name_cpp  
47/53 Test #47: _home_jcelerier_projets_oss_boost_pfr_test_core_name_print_name_cpp ............................................   Passed    0.00 sec  
      Start 48: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_fields_names_cpp  
48/53 Test #48: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_fields_names_cpp ......................................   Passed    0.00 sec  
      Start 49: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_fields_names_big_cpp  
49/53 Test #49: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_fields_names_big_cpp ..................................   Passed    0.00 sec  
      Start 50: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_fields_names_constexpr_cpp  
50/53 Test #50: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_fields_names_constexpr_cpp ............................   Passed    0.00 sec  
      Start 51: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_fields_names_internal_parser_cpp  
51/53 Test #51: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_fields_names_internal_parser_cpp ......................   Passed    0.00 sec  
      Start 52: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_fields_names_nonascii_cpp  
52/53 Test #52: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_fields_names_nonascii_cpp .............................   Passed    0.00 sec  
      Start 53: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_for_each_field_with_name_cpp  
53/53 Test #53: _home_jcelerier_projets_oss_boost_pfr_test_core_name_run_for_each_field_with_name_cpp ..........................   Passed    0.00 sec  
  
100% tests passed, 0 tests failed out of 53  
  
```

---

## Comment 1

> Username: jcelerier  
> Created_at: 2025-03-02 15:55:06 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#issuecomment-2692792132  

I'd like to add such a CI configuration, though IDK if there is some github action or VM that easily provides clang@HEAD?

---

## Comment 2

> Username: coveralls  
> Created_at: 2025-03-02 16:00:56 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#issuecomment-2692794219  

## Pull Request Test Coverage Report for [Build 13617014832](https://coveralls.io/builds/72500174)  
  
  
### Details  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/72500174/badge)](https://coveralls.io/builds/72500174) |  
| :-- | --: |  
| Change from base [Build 12675824451](https://coveralls.io/builds/71641678): |  0.0% |  
| Covered Lines: | 407 |  
| Relevant Lines: | 407 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Review 3 [Changes requested]

> Username: denzor200  
> Created_at: 2025-03-02 19:32:34 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/194#pullrequestreview-2652799434  

📁 include/boost/pfr/detail/core.hpp

```diff
  16 |- #if BOOST_PFR_USE_CPP17
  16 |+ #if BOOST_PFR_USE_CPP26
  17 |+ #include <boost/pfr/detail/core26.hpp>
```

> Username: denzor200  
> Created_at: 2025-03-02 19:32:28 UTC  
> Updated_at: 2025-03-02 19:32:35 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#discussion_r1976698943  

forgot to push core26.hpp ?

> Username: jcelerier  
> Created_at: 2025-03-02 20:42:19 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#discussion_r1976711743  

indeed :slightly_frowning_face: fixed !


---

## Review 4 [Changes requested]

> Username: denzor200  
> Created_at: 2025-03-02 21:45:12 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/194#pullrequestreview-2652824667  

📁 include/boost/pfr/core.hpp

```diff
  54 |+ #else
  55 |+     return detail::sequence_tuple::get<I>(detail::tie_as_tuple(val));
  56 |+ #endif
```

> Username: denzor200  
> Created_at: 2025-03-02 21:33:05 UTC  
> Updated_at: 2025-03-02 21:45:12 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#discussion_r1976722989  

Redundant change, please remove it. You've already implemented `detail::tie_as_tuple` which is responsible for that

---

📁 include/boost/pfr/core.hpp

```diff
  69 |+ #else
  70 |     return detail::sequence_tuple::get<I>( detail::tie_as_tuple(val) );
  71 |+ #endif
```

> Username: denzor200  
> Created_at: 2025-03-02 21:33:23 UTC  
> Updated_at: 2025-03-02 21:45:12 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#discussion_r1976723023  

Redundant change, please remove it. You've already implemented `detail::tie_as_tuple` which is responsible for that

---

📁 include/boost/pfr/core.hpp

```diff
  90 |+ #else
  91 |     return std::move(detail::sequence_tuple::get<I>( detail::tie_as_tuple(val) ));
  92 |+ #endif
```

> Username: denzor200  
> Created_at: 2025-03-02 21:33:33 UTC  
> Updated_at: 2025-03-02 21:45:12 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#discussion_r1976723039  

Redundant change, please remove it. You've already implemented `detail::tie_as_tuple` which is responsible for that

---

📁 include/boost/pfr/core.hpp

```diff
 167 |         detail::make_index_sequence< tuple_size_v<T> >()
 168 |     );
 169 |+ #endif
```

> Username: denzor200  
> Created_at: 2025-03-02 21:33:44 UTC  
> Updated_at: 2025-03-02 21:45:12 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#discussion_r1976723066  

Redundant change, please remove it. You've already implemented `detail::tie_as_tuple` which is responsible for that

---

📁 include/boost/pfr/core.hpp

```diff
 197 |         detail::make_index_sequence< tuple_size_v<T> >()
 198 |     );
 199 |+ #endif
```

> Username: denzor200  
> Created_at: 2025-03-02 21:33:58 UTC  
> Updated_at: 2025-03-02 21:45:12 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#discussion_r1976723137  

Redundant change, please remove it. You've already implemented `detail::tie_as_tuple` which is responsible for that

---

📁 include/boost/pfr/core.hpp

```diff
 210 |+ #if BOOST_PFR_USE_CPP26
 211 |+     auto &[... members] = val;
 212 |+     return std::tie(std::forward_like<T &>(members)...);
```

> Username: denzor200  
> Created_at: 2025-03-02 21:34:13 UTC  
> Updated_at: 2025-03-02 21:45:12 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#discussion_r1976723193  

Redundant change, please remove it. You've already implemented `detail::tie_as_tuple` which is responsible for that


📁 include/boost/pfr/detail/core26.hpp

```diff
  21 |+     return sequence_tuple::tuple<std::add_lvalue_reference_t<decltype(members)>...>{members...};
  22 |+ }
  23 |+ 
```

> Username: denzor200  
> Created_at: 2025-03-02 21:39:35 UTC  
> Updated_at: 2025-03-02 21:45:12 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#discussion_r1976724143  

Please implement `for_each_field_dispatcher` in order to fit the core interface.


📁 include/boost/pfr/detail/for_each_field.hpp

```diff
  39 |+                                                   std::is_rvalue_reference<T &&>{});
  40 |+     }
  41 |+ #else
```

> Username: denzor200  
> Created_at: 2025-03-02 21:44:50 UTC  
> Updated_at: 2025-03-02 21:45:12 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#discussion_r1976725185  

Redundant change, please remove it. You should implement `detail::for_each_field_dispatcher` as it discussed in another thread, which is responsible for that.


---

## Review 5 [Changes requested]

> Username: denzor200  
> Created_at: 2025-03-02 21:48:06 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/194#pullrequestreview-2652827824  

📁 include/boost/pfr/detail/for_each_field_impl.hpp

```diff
  24 | using size_t_ = std::integral_constant<std::size_t, Index >;
  25 | 
  26 |+ #if BOOST_PFR_USE_CPP26
```

> Username: denzor200  
> Created_at: 2025-03-02 21:48:01 UTC  
> Updated_at: 2025-03-02 21:48:06 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#discussion_r1976725752  

It becomens redundat having you removed the change in `for_each_field.hpp`. Please remove this change too


---

## Comment 6

> Username: apolukhin  
> Created_at: 2025-09-07 12:48:54 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#issuecomment-3263752144  

I think  this is fine. Multiple BOOST_PFR_USE_CPP26 checks reduce template instantiations count that would otherwise happen with for_each_field_dispatcher usage. In other words `BOOST_PFR_USE_CPP26` checks are goot for compile time  
  
Will merge the PR in https://github.com/boostorg/pfr/pull/220 after few additions to tests.

---

## Comment 7

> Username: apolukhin  
> Created_at: 2025-09-07 17:27:45 UTC  
> Url: https://github.com/boostorg/pfr/pull/194#issuecomment-3263922874  

Merged in https://github.com/boostorg/pfr/commit/d9fde1f2a0ab92e7db204c49c1612536cb296dad  
  
Many thanks for the PR!

---
