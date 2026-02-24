# #126 Features/subtensor [Open]

> Username: amitsingh19975  
> Created at: 2021-06-21 05:32:39 UTC  
> Updated at: 2023-05-28 09:11:46 UTC  
> Url: https://github.com/boostorg/ublas/pull/126  



---

## Review 1 [Commented]

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/126#pullrequestreview-688057117  

Reports by clang tidy

📁 test/tensor/test_access.cpp

```diff
  37 |+   fixture()
  38 |+   {
  39 |+     static_assert(shapes.size() == multi_index.size(),"");
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:09 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097652  

use unary `static_assert` when the string literal is an empty string [modernize-unary-static-assert]  
```cpp  
static_assert(shapes.size() == multi_index.size(),"");  
    ^                                                 ~~  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_access.cpp

```diff
  38 |+   {
  39 |+     static_assert(shapes.size() == multi_index.size(),"");
  40 |+     static_assert(shapes.size() == indexf.size(),"");
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:09 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097655  

use unary `static_assert` when the string literal is an empty string [modernize-unary-static-assert]  
```cpp  
static_assert(shapes.size() == indexf.size(),"");  
    ^                                            ~~  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_access.cpp

```diff
  39 |+     static_assert(shapes.size() == multi_index.size(),"");
  40 |+     static_assert(shapes.size() == indexf.size(),"");
  41 |+     static_assert(shapes.size() == indexl.size(),"");
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:09 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097658  

use unary `static_assert` when the string literal is an empty string [modernize-unary-static-assert]  
```cpp  
static_assert(shapes.size() == indexl.size(),"");  
    ^                                            ~~  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_access.cpp

```diff
  40 |+     static_assert(shapes.size() == indexf.size(),"");
  41 |+     static_assert(shapes.size() == indexl.size(),"");
  42 |+     static_assert(shapes.size() == ranks.size(),"");
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:09 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097660  

use unary `static_assert` when the string literal is an empty string [modernize-unary-static-assert]  
```cpp  
static_assert(shapes.size() == ranks.size(),"");  
    ^                                           ~~  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_access.cpp

```diff
  53 |+ 
  54 |+ 
  55 |+   static inline auto shapes = std::array<extents_t,15>
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:09 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097663  

15 is a magic number; consider replacing it with a named constant [cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers]  
```cpp  
static inline auto shapes = std::array<extents_t,15>  
                                                   ^  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_access.cpp

```diff
 186 |+     auto const& w    = ub::to_strides(n,layout_t{});
 187 |+     auto const& i    = std::get<I>(multi_index);
 188 |+     auto const& jref = std::get<I>(index);
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:09 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097667  

1st function call argument is an uninitialized value [clang-analyzer-core.CallAndMessage]  
```cpp  
auto const& jref = std::get<I>(index);  
                       ^  
test/tensor/test_access.cpp:175:1: note: Calling 'test_compute_single_index::test_method'  
BOOST_FIXTURE_TEST_CASE_TEMPLATE( test_compute_single_index, layout_t,  layout_types, fixture )  
^  
/usr/include/boost/test/unit_test_suite.hpp:244:9: note: expanded from macro 'BOOST_FIXTURE_TEST_CASE_TEMPLATE'  
        t.test_method();                                                \  
        ^  
test/tensor/test_access.cpp:182:33: note: 'is_first_order' is false  
  constexpr auto const& index = is_first_order ? indexf : indexl;  
                                ^  
test/tensor/test_access.cpp:182:33: note: '?' condition is false  
test/tensor/test_access.cpp:184:3: note: Uninitialized value stored to 'index'  
  mp::mp_for_each<mp::mp_iota_c<std::size(index)>>( [&]( auto I ) {  
  ^  
test/tensor/test_access.cpp:184:3: note: Calling 'mp_for_each<boost::mp11::mp_list<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>, std::integral_constant<unsigned long, 3>, std::integral_constant<unsigned long, 4>, std::integral_constant<unsigned long, 5>, std::integral_constant<unsigned long, 6>, std::integral_constant<unsigned long, 7>, std::integral_constant<unsigned long, 8>, std::integral_constant<unsigned long, 9>, std::integral_constant<unsigned long, 10>, std::integral_constant<unsigned long, 11>, std::integral_constant<unsigned long, 12>, std::integral_constant<unsigned long, 13>, std::integral_constant<unsigned long, 14>>, (lambda at test/tensor/test_access.cpp:184:53)>'  
/usr/include/boost/mp11/algorithm.hpp:1072:12: note: Calling 'mp_for_each_impl<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>, std::integral_constant<unsigned long, 3>, std::integral_constant<unsigned long, 4>, std::integral_constant<unsigned long, 5>, std::integral_constant<unsigned long, 6>, std::integral_constant<unsigned long, 7>, std::integral_constant<unsigned long, 8>, std::integral_constant<unsigned long, 9>, std::integral_constant<unsigned long, 10>, std::integral_constant<unsigned long, 11>, std::integral_constant<unsigned long, 12>, std::integral_constant<unsigned long, 13>, std::integral_constant<unsigned long, 14>, (lambda at test/tensor/test_access.cpp:184:53)>'  
    return detail::mp_for_each_impl( mp_rename<L, mp_list>(), std::forward<F>(f) );  
           ^  
/usr/include/boost/mp11/algorithm.hpp:1039:28: note: Calling 'operator()'  
    return (void)A{ ((void)f(T()), 0)... }, std::forward<F>(f);  
                           ^  
test/tensor/test_access.cpp:188:24: note: 1st function call argument is an uninitialized value  
    auto const& jref = std::get<I>(index);  
                       ^  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_access.cpp

```diff
 188 |+     auto const& jref = std::get<I>(index);
 189 |+     mp::mp_for_each<mp::mp_iota_c<std::size(i)>>( [&]( auto K ) {
 190 |+       auto const& ii = std::get<K>(i);
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:09 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097669  

1st function call argument is an uninitialized value [clang-analyzer-core.CallAndMessage]  
```cpp  
auto const& ii = std::get<K>(i);  
                       ^  
test/tensor/test_access.cpp:188:24: note: Uninitialized value stored to 'i'  
    auto const& jref = std::get<I>(index);  
                       ^  
test/tensor/test_access.cpp:189:5: note: Calling 'mp_for_each<boost::mp11::mp_list<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>>, (lambda at test/tensor/test_access.cpp:189:51)>'  
    mp::mp_for_each<mp::mp_iota_c<std::size(i)>>( [&]( auto K ) {  
    ^  
/usr/include/boost/mp11/algorithm.hpp:1072:12: note: Calling 'mp_for_each_impl<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>, (lambda at test/tensor/test_access.cpp:189:51)>'  
    return detail::mp_for_each_impl( mp_rename<L, mp_list>(), std::forward<F>(f) );  
           ^  
/usr/include/boost/mp11/algorithm.hpp:1039:28: note: Calling 'operator()'  
    return (void)A{ ((void)f(T()), 0)... }, std::forward<F>(f);  
                           ^  
test/tensor/test_access.cpp:190:24: note: 1st function call argument is an uninitialized value  
      auto const& ii = std::get<K>(i);  
                       ^  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_access.cpp

```diff
 209 |+     auto const& w    = ub::to_strides(n,layout_t{});
 210 |+     auto const& i    = std::get<I>(multi_index);
 211 |+     auto const& jref = std::get<I>(index);
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:09 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097672  

1st function call argument is an uninitialized value [clang-analyzer-core.CallAndMessage]  
```cpp  
auto const& jref = std::get<I>(index);  
                       ^  
test/tensor/test_access.cpp:199:1: note: Calling 'test_compute_single_index_static_rank::test_method'  
BOOST_FIXTURE_TEST_CASE_TEMPLATE( test_compute_single_index_static_rank, layout_t,  layout_types, fixture )  
^  
/usr/include/boost/test/unit_test_suite.hpp:244:9: note: expanded from macro 'BOOST_FIXTURE_TEST_CASE_TEMPLATE'  
        t.test_method();                                                \  
        ^  
test/tensor/test_access.cpp:205:33: note: 'is_first_order' is false  
  constexpr auto const& index = is_first_order ? indexf : indexl;  
                                ^  
test/tensor/test_access.cpp:205:33: note: '?' condition is false  
test/tensor/test_access.cpp:207:3: note: Uninitialized value stored to 'index'  
  mp::mp_for_each<mp::mp_iota_c<std::size(index)>>( [&]( auto I ) {  
  ^  
test/tensor/test_access.cpp:207:3: note: Calling 'mp_for_each<boost::mp11::mp_list<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>, std::integral_constant<unsigned long, 3>, std::integral_constant<unsigned long, 4>, std::integral_constant<unsigned long, 5>, std::integral_constant<unsigned long, 6>, std::integral_constant<unsigned long, 7>, std::integral_constant<unsigned long, 8>, std::integral_constant<unsigned long, 9>, std::integral_constant<unsigned long, 10>, std::integral_constant<unsigned long, 11>, std::integral_constant<unsigned long, 12>, std::integral_constant<unsigned long, 13>, std::integral_constant<unsigned long, 14>>, (lambda at test/tensor/test_access.cpp:207:53)>'  
/usr/include/boost/mp11/algorithm.hpp:1072:12: note: Calling 'mp_for_each_impl<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>, std::integral_constant<unsigned long, 3>, std::integral_constant<unsigned long, 4>, std::integral_constant<unsigned long, 5>, std::integral_constant<unsigned long, 6>, std::integral_constant<unsigned long, 7>, std::integral_constant<unsigned long, 8>, std::integral_constant<unsigned long, 9>, std::integral_constant<unsigned long, 10>, std::integral_constant<unsigned long, 11>, std::integral_constant<unsigned long, 12>, std::integral_constant<unsigned long, 13>, std::integral_constant<unsigned long, 14>, (lambda at test/tensor/test_access.cpp:207:53)>'  
    return detail::mp_for_each_impl( mp_rename<L, mp_list>(), std::forward<F>(f) );  
           ^  
/usr/include/boost/mp11/algorithm.hpp:1039:28: note: Calling 'operator()'  
    return (void)A{ ((void)f(T()), 0)... }, std::forward<F>(f);  
                           ^  
test/tensor/test_access.cpp:211:24: note: 1st function call argument is an uninitialized value  
    auto const& jref = std::get<I>(index);  
                       ^  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_access.cpp

```diff
 212 |+     mp::mp_for_each<mp::mp_iota_c<std::size(i)>>( [&]( auto K ) {
 213 |+       constexpr auto r = std::get<I>(ranks);
 214 |+       auto const& ii = std::get<K>(i);
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:10 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097674  

1st function call argument is an uninitialized value [clang-analyzer-core.CallAndMessage]  
```cpp  
auto const& ii = std::get<K>(i);  
                       ^  
test/tensor/test_access.cpp:211:24: note: Uninitialized value stored to 'i'  
    auto const& jref = std::get<I>(index);  
                       ^  
test/tensor/test_access.cpp:212:5: note: Calling 'mp_for_each<boost::mp11::mp_list<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>>, (lambda at test/tensor/test_access.cpp:212:51)>'  
    mp::mp_for_each<mp::mp_iota_c<std::size(i)>>( [&]( auto K ) {  
    ^  
/usr/include/boost/mp11/algorithm.hpp:1072:12: note: Calling 'mp_for_each_impl<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>, (lambda at test/tensor/test_access.cpp:212:51)>'  
    return detail::mp_for_each_impl( mp_rename<L, mp_list>(), std::forward<F>(f) );  
           ^  
/usr/include/boost/mp11/algorithm.hpp:1039:28: note: Calling 'operator()'  
    return (void)A{ ((void)f(T()), 0)... }, std::forward<F>(f);  
                           ^  
test/tensor/test_access.cpp:214:24: note: 1st function call argument is an uninitialized value  
      auto const& ii = std::get<K>(i);  
                       ^  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_access.cpp

```diff
 262 |+         auto const& n    = std::get<I>(shapes);
 263 |+         auto const& iref = std::get<I>(multi_index);
 264 |+         auto const& jref = std::get<I>(index);
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:10 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097681  

1st function call argument is an uninitialized value [clang-analyzer-core.CallAndMessage]  
```cpp  
auto const& jref = std::get<I>(index);  
                           ^  
test/tensor/test_access.cpp:252:1: note: Calling 'test_compute_multi_index_static_rank::test_method'  
BOOST_FIXTURE_TEST_CASE_TEMPLATE( test_compute_multi_index_static_rank, layout_t,  layout_types, fixture )  
^  
/usr/include/boost/test/unit_test_suite.hpp:244:9: note: expanded from macro 'BOOST_FIXTURE_TEST_CASE_TEMPLATE'  
        t.test_method();                                                \  
        ^  
test/tensor/test_access.cpp:258:35: note: 'is_first_order' is false  
    constexpr auto const& index = is_first_order ? indexf : indexl;  
                                  ^  
test/tensor/test_access.cpp:258:35: note: '?' condition is false  
test/tensor/test_access.cpp:261:5: note: Uninitialized value stored to 'index'  
    mp::mp_for_each<mp::mp_iota_c<std::size(index)>>( [&]( auto I ) {  
    ^  
test/tensor/test_access.cpp:261:5: note: Calling 'mp_for_each<boost::mp11::mp_list<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>, std::integral_constant<unsigned long, 3>, std::integral_constant<unsigned long, 4>, std::integral_constant<unsigned long, 5>, std::integral_constant<unsigned long, 6>, std::integral_constant<unsigned long, 7>, std::integral_constant<unsigned long, 8>, std::integral_constant<unsigned long, 9>, std::integral_constant<unsigned long, 10>, std::integral_constant<unsigned long, 11>, std::integral_constant<unsigned long, 12>, std::integral_constant<unsigned long, 13>, std::integral_constant<unsigned long, 14>>, (lambda at test/tensor/test_access.cpp:261:55)>'  
/usr/include/boost/mp11/algorithm.hpp:1072:12: note: Calling 'mp_for_each_impl<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>, std::integral_constant<unsigned long, 3>, std::integral_constant<unsigned long, 4>, std::integral_constant<unsigned long, 5>, std::integral_constant<unsigned long, 6>, std::integral_constant<unsigned long, 7>, std::integral_constant<unsigned long, 8>, std::integral_constant<unsigned long, 9>, std::integral_constant<unsigned long, 10>, std::integral_constant<unsigned long, 11>, std::integral_constant<unsigned long, 12>, std::integral_constant<unsigned long, 13>, std::integral_constant<unsigned long, 14>, (lambda at test/tensor/test_access.cpp:261:55)>'  
    return detail::mp_for_each_impl( mp_rename<L, mp_list>(), std::forward<F>(f) );  
           ^  
/usr/include/boost/mp11/algorithm.hpp:1039:28: note: Calling 'operator()'  
    return (void)A{ ((void)f(T()), 0)... }, std::forward<F>(f);  
                           ^  
test/tensor/test_access.cpp:264:28: note: 1st function call argument is an uninitialized value  
        auto const& jref = std::get<I>(index);  
                           ^  
```  
Reported as warning by clang-tidy.

---

📁 test/tensor/test_access.cpp

```diff
 265 |+         auto const& w    = ub::to_strides(n,layout_t{});        
 266 |+         mp::mp_for_each<mp::mp_iota_c<std::size(iref)>>( [&]( auto K ) {
 267 |+             auto const  jj = std::get<K>(jref);
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:10 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097682  

1st function call argument is an uninitialized value [clang-analyzer-core.CallAndMessage]  
```cpp  
auto const  jj = std::get<K>(jref);  
                             ^  
test/tensor/test_access.cpp:265:28: note: Uninitialized value stored to 'jref'  
        auto const& w    = ub::to_strides(n,layout_t{});          
                           ^  
test/tensor/test_access.cpp:266:9: note: Calling 'mp_for_each<boost::mp11::mp_list<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>>, (lambda at test/tensor/test_access.cpp:266:58)>'  
        mp::mp_for_each<mp::mp_iota_c<std::size(iref)>>( [&]( auto K ) {  
        ^  
/usr/include/boost/mp11/algorithm.hpp:1072:12: note: Calling 'mp_for_each_impl<std::integral_constant<unsigned long, 0>, std::integral_constant<unsigned long, 1>, std::integral_constant<unsigned long, 2>, (lambda at test/tensor/test_access.cpp:266:58)>'  
    return detail::mp_for_each_impl( mp_rename<L, mp_list>(), std::forward<F>(f) );  
           ^  
/usr/include/boost/mp11/algorithm.hpp:1039:28: note: Calling 'operator()'  
    return (void)A{ ((void)f(T()), 0)... }, std::forward<F>(f);  
                           ^  
test/tensor/test_access.cpp:267:30: note: 1st function call argument is an uninitialized value  
            auto const  jj = std::get<K>(jref);  
                             ^  
```  
Reported as warning by clang-tidy.


📁 test/tensor/test_span.cpp

```diff
 236 |+ BOOST_FIXTURE_TEST_CASE( ran_test, fixture )
 237 |+ {
 238 |+ 	using namespace boost::numeric::ublas;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:10 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097683  

do not use namespace using-directives; use using-declarations instead [google-build-using-namespace]  
```cpp  
using namespace boost::numeric::ublas;  
        ^  
```  
Reported as warning by clang-tidy.


📁 test/tensor/test_subtensor_utility.cpp

```diff
 102 |+ {
 103 |+ 
 104 |+ 	using namespace boost::numeric;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:10 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097685  

do not use namespace using-directives; use using-declarations instead [google-build-using-namespace]  
```cpp  
using namespace boost::numeric;  
        ^  
```  
Reported as warning by clang-tidy.


📁 include/boost/numeric/ublas/tensor/access.hpp

```diff
  55 |+ {
  56 |+        if constexpr(p==0u) return 0ul;
  57 |+   else if constexpr(p >1u) return compute_single_index<p-1>(i,i,w)+i[p-1]*w[p-1];
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:11 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097686  

different indentation for `if` and corresponding `else` [readability-misleading-indentation]  
```cpp  
else if constexpr(p >1u) return compute_single_index<p-1>(i,i,w)+i[p-1]*w[p-1];  
  ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/access.hpp

```diff
  56 |+        if constexpr(p==0u) return 0ul;
  57 |+   else if constexpr(p >1u) return compute_single_index<p-1>(i,i,w)+i[p-1]*w[p-1];
  58 |+   else                     return i[p-1]*w[p-1];
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:11 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097688  

different indentation for `if` and corresponding `else` [readability-misleading-indentation]  
```cpp  
else                     return i[p-1]*w[p-1];  
  ^  
```  
Reported as warning by clang-tidy.


📁 include/boost/numeric/ublas/tensor/extents/extents_functions.hpp

```diff
  87 |+       cbegin(e)[1] >  1ul &&
  88 |+       std::all_of(cbegin(e)+2ul,cend  (e) , [](auto a){return a==1ul;})){
  89 |+     return true;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:11 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097689  

redundant boolean literal in conditional return statement [readability-simplify-boolean-expr]  
```cpp  
return true;  
~~~~~~~~~~~^~~~~  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/extents/extents_functions.hpp

```diff
 103 |+       cbegin(e)[1] == 1ul &&
 104 |+       std::all_of(cbegin(e)+2ul,cend  (e) , [](auto a){return a==1ul;})){
 105 |+     return true;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:11 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097690  

redundant boolean literal in conditional return statement [readability-simplify-boolean-expr]  
```cpp  
return true;  
~~~~~~~~~~~^~~~~  
```  
Reported as warning by clang-tidy.


📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
  95 |+ 	// covers only one index of one dimension
  96 |+ 	// e.g. a(1) or a(end)
  97 |+ 	span(value_type n)
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:11 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097692  

single-argument constructors must be marked explicit to avoid unintentional implicit conversions [hicpp-explicit-conversions]  
```cpp  
span(value_type n)  
        ^  
        explicit  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 100 |+ 	}
 101 |+ 
 102 |+ 	span(span const& other)
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:11 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097694  

use `= default` to define a trivial copy constructor [hicpp-use-equals-default,modernize-use-equals-default]  
```cpp  
span(span const& other)  
        ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 108 |+ 	}
 109 |+ 
 110 |+ 	span& operator=(span const& other)
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:11 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097697  

use `= default` to define a trivial copy-assignment operator [hicpp-use-equals-default,modernize-use-equals-default]  
```cpp  
span& operator=(span const& other)  
              ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 117 |+ 	}
 118 |+ 
 119 |+   inline auto first() const {return first_; }
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:11 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097700  

function `first` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
inline auto first() const {return first_; }  
  ^  
  [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 118 |+ 
 119 |+   inline auto first() const {return first_; }
 120 |+   inline auto last () const {return last_ ; }
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:11 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097702  

function `last` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
inline auto last () const {return last_ ; }  
  ^  
  [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 119 |+   inline auto first() const {return first_; }
 120 |+   inline auto last () const {return last_ ; }
 121 |+   inline auto step () const {return step_ ; }
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:12 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097704  

function `step` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
inline auto step () const {return step_ ; }  
  ^  
  [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 120 |+   inline auto last () const {return last_ ; }
 121 |+   inline auto step () const {return step_ ; }
 122 |+   inline auto size () const {return size_ ; }
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:12 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097705  

function `size` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
inline auto size () const {return size_ ; }  
  ^  
  [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 140 |+ protected:
 141 |+ 
 142 |+ 	value_type first_, last_ , step_, size_;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:12 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097706  

member variable `first_` has protected visibility [cppcoreguidelines-non-private-member-variables-in-classes]  
```cpp  
value_type first_, last_ , step_, size_;  
                   ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 140 |+ protected:
 141 |+ 
 142 |+ 	value_type first_, last_ , step_, size_;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:12 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097707  

use default member initializer for `first_` [modernize-use-default-member-init]  
```cpp  
value_type first_, last_ , step_, size_;  
                   ^  
                         {}  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 140 |+ protected:
 141 |+ 
 142 |+ 	value_type first_, last_ , step_, size_;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:12 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097709  

member variable `last_` has protected visibility [cppcoreguidelines-non-private-member-variables-in-classes]  
```cpp  
value_type first_, last_ , step_, size_;  
                           ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 140 |+ protected:
 141 |+ 
 142 |+ 	value_type first_, last_ , step_, size_;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:12 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097711  

use default member initializer for `last_` [modernize-use-default-member-init]  
```cpp  
value_type first_, last_ , step_, size_;  
                           ^  
                                {}  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 140 |+ protected:
 141 |+ 
 142 |+ 	value_type first_, last_ , step_, size_;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:12 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097713  

member variable `step_` has protected visibility [cppcoreguidelines-non-private-member-variables-in-classes]  
```cpp  
value_type first_, last_ , step_, size_;  
                                   ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 140 |+ protected:
 141 |+ 
 142 |+ 	value_type first_, last_ , step_, size_;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:12 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097715  

use default member initializer for `step_` [modernize-use-default-member-init]  
```cpp  
value_type first_, last_ , step_, size_;  
                                   ^  
                                        {}  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 140 |+ protected:
 141 |+ 
 142 |+ 	value_type first_, last_ , step_, size_;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:12 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097716  

member variable `size_` has protected visibility [cppcoreguidelines-non-private-member-variables-in-classes]  
```cpp  
value_type first_, last_ , step_, size_;  
                                          ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 140 |+ protected:
 141 |+ 
 142 |+ 	value_type first_, last_ , step_, size_;
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:12 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097719  

use default member initializer for `size_` [modernize-use-default-member-init]  
```cpp  
value_type first_, last_ , step_, size_;  
                                          ^  
                                               {}  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 145 |+ using strided_span = span<tag::strided, std::size_t>;
 146 |+ 
 147 |+ } // namespace
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:13 UTC  
> Updated_at: 2021-06-21 06:08:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097720  

namespace `boost::numeric::ublas` ends with a comment that refers to a wrong namespace `` [google-readability-namespace-comments]  
```cpp  
} // namespace  
 ^~~~~~~~~~~~~  
   // namespace boost::numeric::ublas  
include/boost/numeric/ublas/tensor/span.hpp:32:11: note: namespace 'boost::numeric::ublas' starts here  
namespace boost::numeric::ublas {  
          ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 161 |+ 	using value_type = typename super_type::value_type;
 162 |+ 	constexpr explicit span()
 163 |+ 		: super_type()
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:13 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097722  

initializer for base class `span<tag::strided, std::size_t>` (aka `span<boost::numeric::ublas::tag::strided, unsigned long>`) is redundant [readability-redundant-member-init]  
```cpp  
: super_type()  
                  ^~~~~~~~~~~~  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 170 |+ 	}
 171 |+ 
 172 |+ 	span(value_type n)
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:13 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097724  

single-argument constructors must be marked explicit to avoid unintentional implicit conversions [hicpp-explicit-conversions]  
```cpp  
span(value_type n)  
        ^  
        explicit  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 175 |+ 	}
 176 |+ 
 177 |+ 	span(span const& other)
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:13 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097727  

use `= default` to define a trivial copy constructor [hicpp-use-equals-default,modernize-use-equals-default]  
```cpp  
span(span const& other)  
        ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 193 |+ 	}
 194 |+ 
 195 |+   inline auto first() const {return super_type::first(); }
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:13 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097728  

function `first` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
inline auto first() const {return super_type::first(); }  
  ^  
  [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 194 |+ 
 195 |+   inline auto first() const {return super_type::first(); }
 196 |+   inline auto last () const {return super_type::last (); }
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:13 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097731  

function `last` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
inline auto last () const {return super_type::last (); }  
  ^  
  [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 195 |+   inline auto first() const {return super_type::first(); }
 196 |+   inline auto last () const {return super_type::last (); }
 197 |+   inline auto step () const {return super_type::step (); }
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:13 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097733  

function `step` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
inline auto step () const {return super_type::step (); }  
  ^  
  [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 196 |+   inline auto last () const {return super_type::last (); }
 197 |+   inline auto step () const {return super_type::step (); }
 198 |+   inline auto size () const {return super_type::size (); }
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:13 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097736  

function `size` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
inline auto size () const {return super_type::size (); }  
  ^  
  [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 220 |+ }
 221 |+ 
 222 |+ } // namespace
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:13 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097738  

namespace `boost::numeric::ublas` ends with a comment that refers to a wrong namespace `` [google-readability-namespace-comments]  
```cpp  
} // namespace  
 ^~~~~~~~~~~~~  
   // namespace boost::numeric::ublas  
include/boost/numeric/ublas/tensor/span.hpp:152:11: note: namespace 'boost::numeric::ublas' starts here  
namespace boost::numeric::ublas {  
          ^  
```  
Reported as warning by clang-tidy.


📁 include/boost/numeric/ublas/tensor/subtensor.hpp

```diff
 116 |+ 	 */
 117 |+ 	BOOST_UBLAS_INLINE
 118 |+ 	subtensor (tensor_type& t)
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:13 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097740  

single-argument constructors must be marked explicit to avoid unintentional implicit conversions [hicpp-explicit-conversions]  
```cpp  
subtensor (tensor_type& t)  
        ^  
        explicit  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/subtensor.hpp

```diff
 118 |+ 	subtensor (tensor_type& t)
 119 |+ 		: super_type    ()
 120 |+ 		, spans_        ()
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:14 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097742  

initializer for member `spans_` is redundant [readability-redundant-member-init]  
```cpp  
, spans_        ()  
                  ^~~~~~~~~~~~~~~~  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/subtensor.hpp

```diff
 127 |+ 
 128 |+ 	template<typename ... span_types>
 129 |+ 	subtensor(tensor_type& t, span_types&& ... spans)
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:14 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097745  

constructors that are callable with a single argument must be marked explicit to avoid unintentional implicit conversions [hicpp-explicit-conversions]  
```cpp  
subtensor(tensor_type& t, span_types&& ... spans)  
        ^  
        explicit  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/subtensor.hpp

```diff
 149 |+   subtensor (tensor_type const& t)
 150 |+     : super_type    ()
 151 |+     , spans_        ()
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:14 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097747  

initializer for member `spans_` is redundant [readability-redundant-member-init]  
```cpp  
, spans_        ()  
      ^~~~~~~~~~~~~~~~  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/subtensor.hpp

```diff
 179 |+ 	 */
 180 |+ 	BOOST_UBLAS_INLINE
 181 |+   subtensor (subtensor &&v)
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:14 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097751  

move constructors should be marked noexcept [hicpp-noexcept-move,performance-noexcept-move-constructor]  
```cpp  
subtensor (subtensor &&v)  
  ^  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/subtensor.hpp

```diff
 401 |+ 
 402 |+ 	/** @brief Returns the span strides of the subtensor */
 403 |+   inline auto const& span_strides () const {
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:14 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097752  

function `span_strides` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
inline auto const& span_strides () const {  
  ^  
  [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/subtensor.hpp

```diff
 406 |+ 
 407 |+ 	/** @brief Returns the span strides of the subtensor */
 408 |+   inline auto const& spans () const {
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:14 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097753  

function `spans` should be marked [[nodiscard]] [modernize-use-nodiscard]  
```cpp  
inline auto const& spans () const {  
  ^  
  [[nodiscard]]  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/subtensor.hpp

```diff
 673 |+ 
 674 |+ 
 675 |+ } // namespaces boost::numeric::ublas
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:14 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097754  

namespace `boost::numeric::ublas` ends with an unrecognized comment [google-readability-namespace-comments]  
```cpp  
} // namespaces boost::numeric::ublas  
 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   // namespace boost::numeric::ublas  
include/boost/numeric/ublas/tensor/subtensor.hpp:26:11: note: namespace 'boost::numeric::ublas' starts here  
namespace boost::numeric::ublas {  
          ^  
```  
Reported as warning by clang-tidy.


📁 include/boost/numeric/ublas/tensor/subtensor_utility.hpp

```diff
  14 |+ 
  15 |+ #ifndef _BOOST_NUMERIC_UBLAS_TENSOR_SUBTENSOR_UTILITY_HPP_
  16 |+ #define _BOOST_NUMERIC_UBLAS_TENSOR_SUBTENSOR_UTILITY_HPP_
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:14 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097756  

declaration uses identifier `_BOOST_NUMERIC_UBLAS_TENSOR_SUBTENSOR_UTILITY_HPP_`, which is a reserved identifier [bugprone-reserved-identifier]  
```cpp  
#define _BOOST_NUMERIC_UBLAS_TENSOR_SUBTENSOR_UTILITY_HPP_  
        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
        BOOST_NUMERIC_UBLAS_TENSOR_SUBTENSOR_UTILITY_HPP_  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/subtensor_utility.hpp

```diff
 119 |+   if constexpr ( is_sliced ){
 120 |+     if(size == 0)        return span_type(0       , extent0);
 121 |+     else if(first== max) return span_type(extent0 , extent0);
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:14 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097759  

do not use `else` after `return` [readability-else-after-return]  
```cpp  
else if(first== max) return span_type(extent0 , extent0);  
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/subtensor_utility.hpp

```diff
 126 |+     size_type step  = s.step ();
 127 |+     if(size == 0)        return span_type(0       , size_type(1), extent0);
 128 |+     else if(first== max) return span_type(extent0 , step, extent0);
```

> Username: github-actions[bot]  
> Created_at: 2021-06-21 06:08:15 UTC  
> Updated_at: 2021-06-21 06:08:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r655097762  

do not use `else` after `return` [readability-else-after-return]  
```cpp  
else if(first== max) return span_type(extent0 , step, extent0);  
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
Reported as warning by clang-tidy.


---

## Review 2 [Commented]

> Username: github-actions[bot]  
> Created_at: 2022-12-11 22:26:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/126#pullrequestreview-1212764487  

Reports by clang tidy

📁 include/boost/numeric/ublas/tensor/span.hpp

```diff
 141 |+ using sspan = span<std::size_t>;
 142 |+ 
 143 |+ } // namespace
```

> Username: github-actions[bot]  
> Created_at: 2022-12-11 22:26:46 UTC  
> Updated_at: 2022-12-11 22:26:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r1045307215  

namespace `boost::numeric::ublas` ends with a comment that refers to a wrong namespace `` [google-readability-namespace-comments]  
```cpp  
} // namespace  
 ^~~~~~~~~~~~~  
   // namespace boost::numeric::ublas  
include/boost/numeric/ublas/tensor/span.hpp:25:11: note: namespace 'boost::numeric::ublas' starts here  
namespace boost::numeric::ublas {  
          ^  
```  
Reported as warning by clang-tidy.


📁 include/boost/numeric/ublas/tensor/subtensor.hpp

```diff
 114 |+ 	subtensor (tensor_type& t)
 115 |+         : super_type    ()
 116 |+         , spans_        ()
```

> Username: github-actions[bot]  
> Created_at: 2022-12-11 22:26:46 UTC  
> Updated_at: 2022-12-11 22:26:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r1045307216  

initializer for member `spans_` is redundant [readability-redundant-member-init]  
```cpp  
, spans_        ()  
          ^~~~~~~~~~~~~~~~  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/subtensor.hpp

```diff
 123 |+ 
 124 |+ 	template<typename ... span_types>
 125 |+     subtensor(tensor_type& t, span_types&& ... spans)
```

> Username: github-actions[bot]  
> Created_at: 2022-12-11 22:26:46 UTC  
> Updated_at: 2022-12-11 22:26:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r1045307217  

constructors that are callable with a single argument must be marked explicit to avoid unintentional implicit conversions [hicpp-explicit-conversions]  
```cpp  
subtensor(tensor_type& t, span_types&& ... spans)  
    ^  
    explicit  
```  
Reported as warning by clang-tidy.

---

📁 include/boost/numeric/ublas/tensor/subtensor.hpp

```diff
 145 |+     subtensor (tensor_type const& t)
 146 |+         : super_type    ()
 147 |+         , spans_        ()
```

> Username: github-actions[bot]  
> Created_at: 2022-12-11 22:26:46 UTC  
> Updated_at: 2022-12-11 22:26:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r1045307218  

initializer for member `spans_` is redundant [readability-redundant-member-init]  
```cpp  
, spans_        ()  
          ^~~~~~~~~~~~~~~~  
```  
Reported as warning by clang-tidy.


📁 include/boost/numeric/ublas/tensor/subtensor_utility.hpp

```diff
 116 |+     size_type step  = s.step ();
 117 |+     if(size == 0)                   return span_type(0       , 1u,   extent0);
 118 |+     else if(first== span_type::max) return span_type(extent0 , step, extent0);
```

> Username: github-actions[bot]  
> Created_at: 2022-12-11 22:26:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#discussion_r1045307220  

do not use `else` after `return` [readability-else-after-return]  
```cpp  
else if(first== span_type::max) return span_type(extent0 , step, extent0);  
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
Reported as warning by clang-tidy.


---

## Comment 3

> Username: freifrauvonbleifrei  
> Created_at: 2023-02-15 14:25:40 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#issuecomment-1431454028  

Hi, I just wanted to let you know that I would be interested in trying this feature!

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2023-05-28 09:11:45 UTC  
> Url: https://github.com/boostorg/ublas/pull/126#issuecomment-1566024613  

This Pull request Passed all of clang-tidy tests. :+1:

---
