# #62 Rotate algorithm and test [Merged]

> Username: roshanrags  
> Created at: 2014-03-10 14:33:23 UTC  
> Updated at: 2014-03-12 17:47:24 UTC  
> Merged at: 2014-03-12 17:38:56 UTC  
> Closed at: 2014-03-12 17:38:56 UTC  
> Url: https://github.com/boostorg/compute/pull/62  



---

## Comment 1

> Username: roshanrags  
> Created_at: 2014-03-10 14:35:54 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37188304  

I couldn't see if test file works, since I am getting a linker error when I compile it using g++. This happens on other test files also. What is the proper way to do this?

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-03-10 15:30:21 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37194994  

Looking good!  
  
For the test you should add the following line to the test/CMakeLists.txt file:  
  
```  
add_compute_test("algorithm.rotate" test_rotate.cpp)  
```  
  
Add it next to the other algorithm tests (I keep them in alphabetical order to find them easier). That will tell cmake to compile the test and run it when `ctest` is called.  
  
We should fix that linker error. Can you post the error messages you get? If it's related to OpenCL functions (e.g. `clGetDeviceIDs`) then I would check that the OpenCL library found and setup properly in cmake (you can edit them with `cmake-gui`). See the [CMake Flags](https://github.com/kylelutz/compute/wiki/Developer's-Guide#wiki-cmake-flags) section under the [Developers Guide](https://github.com/kylelutz/compute/wiki/Developer's-Guide) for more information,

---

## Comment 3

> Username: roshanrags  
> Created_at: 2014-03-10 15:37:51 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37195970  

I am doing  
  
```  
g++ -I../include -I../../Boost -framework OpenCL test_nth_element.cpp  
```  
  
from inside the test directory.  
  
I am getting following linker error:  
  
```  
Undefined symbols for architecture x86_64:  
  "boost::test_tools::tt_detail::check_impl(boost::test_tools::predicate_result const&, boost::unit_test::lazy_ostream const&, boost::unit_test::basic_cstring<char const>, unsigned long, boost::test_tools::tt_detail::tool_level, boost::test_tools::tt_detail::check_type, unsigned long, ...)", referenced from:  
      compute_test::nth_element_int::test_method() in test_nth_element-db1f7V.o  
      bool boost::test_tools::tt_detail::check_frwd<boost::test_tools::tt_detail::equal_impl_frwd, int, int>(boost::test_tools::tt_detail::equal_impl_frwd, boost::unit_test::lazy_ostream const&, boost::unit_test::basic_cstring<char const>, unsigned long, boost::test_tools::tt_detail::tool_level, boost::test_tools::tt_detail::check_type, int const&, char const*, int const&, char const*) in test_nth_element-db1f7V.o  
  "boost::unit_test::unit_test_log_t::set_checkpoint(boost::unit_test::basic_cstring<char const>, unsigned long, boost::unit_test::basic_cstring<char const>)", referenced from:  
      compute_test::nth_element_median::test_method() in test_nth_element-db1f7V.o  
  "boost::unit_test::framework::master_test_suite()", referenced from:  
      init_unit_test_suite(int, char**) in test_nth_element-db1f7V.o  
  "boost::unit_test::test_case::test_case(boost::unit_test::basic_cstring<char const>, boost::unit_test::callback0<boost::unit_test::ut_detail::unused> const&)", referenced from:  
      boost::unit_test::make_test_case(boost::unit_test::callback0<boost::unit_test::ut_detail::unused> const&, boost::unit_test::basic_cstring<char const>) in test_nth_element-db1f7V.o  
  "boost::unit_test::ut_detail::auto_test_unit_registrar::auto_test_unit_registrar(boost::unit_test::basic_cstring<char const>)", referenced from:  
      ___cxx_global_var_init2 in test_nth_element-db1f7V.o  
  "boost::unit_test::ut_detail::auto_test_unit_registrar::auto_test_unit_registrar(boost::unit_test::test_case*, unsigned long)", referenced from:  
      ___cxx_global_var_init4 in test_nth_element-db1f7V.o  
      ___cxx_global_var_init15 in test_nth_element-db1f7V.o  
  "boost::unit_test::ut_detail::auto_test_unit_registrar::auto_test_unit_registrar(int)", referenced from:  
      ___cxx_global_var_init19 in test_nth_element-db1f7V.o  
  "boost::unit_test::ut_detail::normalize_test_case_name(boost::unit_test::basic_cstring<char const>)", referenced from:  
      boost::unit_test::make_test_case(boost::unit_test::callback0<boost::unit_test::ut_detail::unused> const&, boost::unit_test::basic_cstring<char const>) in test_nth_element-db1f7V.o  
  "vtable for boost::unit_test::unit_test_log_t", referenced from:  
      boost::unit_test::unit_test_log_t::unit_test_log_t() in test_nth_element-db1f7V.o  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
  "_main", referenced from:  
     implicit entry/start for main executable  
ld: symbol(s) not found for architecture x86_64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
  
I am guessing the problem has something to do with the unit test framework in Boost not getting linked properly.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-03-10 15:42:03 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37196513  

Ahh, I see. It's preferred to use `cmake` to compile rather directly calling `g++` because it will automatically set up all of the library dependencies and compiler flags for you.  
  
However, you should still be able to make this work. I think you just have to add `-lboost_unit_test_framework` when you call `g++` to link the Boost.Test library.

---

## Comment 5

> Username: roshanrags  
> Created_at: 2014-03-11 15:47:36 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37311081  

I resolved the linking problem. I had actually forgotten to compile/build Boost library. I tried using `cmake-gui` and `ctest` but all tests are failing with `Exception:Other`.

---

## Comment 6

> Username: roshanrags  
> Created_at: 2014-03-11 15:57:36 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37312432  

I enabled BOOST_COMPUTE_BUILD_TESTS and added the root folder of Boost as BOOST_ROOT while doing cmake. Am I missing something? Should compute be inside Boost? Should I give any arguments to `ctest`?

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-03-11 16:25:55 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37316126  

That should be good for cmake. To debug the `Exception:Other` error I'd run the tests in verbose mode to see the output they produce. So, to run the `core.context` tests with output either run `ctest -R core.context -V` or invoke it directly with `./test/test_context`. You can also run all the tests in verbose mode with `ctest -V`. All these should be executed from the build directory.  
  
Most likely the test is failing to find some library and/or symbols it needs so you may have to fix some lookup paths. Post the verbose test output here and I can take a look.

---

## Comment 8

> Username: roshanrags  
> Created_at: 2014-03-11 17:08:17 UTC  
> Updated_at: 2014-03-11 17:14:08 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37321532  

It says `dyld: Library not loaded: libboost_unit_test_framework.dylib` which is strange considering that while doing `cmake`, it said  
  
```  
Found the following Boost libraries:  
  unit_test_framework  
```

---

## Comment 9

> Username: kylelutz  
> Created_at: 2014-03-11 17:22:36 UTC  
> Updated_at: 2014-03-11 17:22:54 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37323405  

Hmm. I don't have much experience with Mac. It looks like `libboost_unit_test_framework` either it's not installed in the correct place or cannot be found by the dynamic linker for some reason. Perhaps try setting the `DYLD_LIBRARY_PATH` environment variable with the full path to `libboost_unit_test_framework.dylib1`. I think this works similarly to `LD_LIBRARY_PATH` on linux.

---

## Comment 10

> Username: roshanrags  
> Created_at: 2014-03-12 11:29:23 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37398332  

Figured it out finally. `otools` said the executable was looking for the library in the same directory, so copied it there and it worked.

---

## Comment 11

> Username: kylelutz  
> Created_at: 2014-03-12 15:30:16 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37422097  

Looks great!  
  
Can you squash the commits down to just one in order to keep the git history in the main branch clean? The simplest way to do this is to run the following commands on your `rotate` branch:  
  
```  
git reset e33d408  
git add .  
git commit --amend  
git push -f origin HEAD  
```  
  
Then it should be good to merge.

---

## Comment 12

> Username: kylelutz  
> Created_at: 2014-03-12 17:39:40 UTC  
> Url: https://github.com/boostorg/compute/pull/62#issuecomment-37439349  

Merged! Thanks for implementing this!  
  
A good follow-up would be to add an implementation of the `rotate_copy()` algorithm (should be very similar and shouldn't require a temporary vector).

---
