# #439 Include <cstddef> in <boost/test/data/size.hpp> [Merged]

> Username: ibis-hdl  
> Created at: 2025-02-02 15:08:00 UTC  
> Updated at: 2025-02-03 18:56:55 UTC  
> Merged at: 2025-02-03 18:56:55 UTC  
> Closed at: 2025-02-03 18:56:55 UTC  
> Url: https://github.com/boostorg/test/pull/439  

If datasets are used, `<boost/test/data/size.hpp>` is also included, as can be seen in the [example](  
    https://www.boost.org/doc/libs/1_87_0/libs/test/doc/html/boost_test/tests_organization/test_cases/test_case_generation/datasets.html#boost_test.tests_organization.test_cases.test_case_generation.datasets.dataset_interface.example_descr). The class `class size_t` uses `std::size_t` in its API.  
MSVC and Gcc indirectly have the definition of `std::size_t`, while Clang/LibC++ do not have this and a compiler error occurs. This fix will correct this.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2025-02-03 18:56:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/test/pull/439#pullrequestreview-2590754908  

Thank you!

---
