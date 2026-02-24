# #405 - Build error for the tests with clang 19 [Closed]

> Username: emmenlau  
> Created at: 2025-01-29 16:01:07 UTC  
> Updated at: 2025-06-05 18:51:38 UTC  
> Closed at: 2025-06-05 18:44:36 UTC  
> Url: https://github.com/boostorg/histogram/issues/405  

In our build system, I'm getting the following error when building the tests for boost 1.87.0 using clang 19.1.7:  
```  
    "clang++"   -std=c++20 -pthread -O3 -fno-inline -Wall -g -m64 --target=x86_64-pc-linux    -DDEBUG  -fdebug-prefix-map=/data/bdaci01/BioDataAnalysis/ci-tmp-U2404Sk64c191/Debug/boost=/home/bdaci01/builds/6b652442/0/BioDataAnalysis/boost    -DDEBUG      -march=skylake -std=gnu++20     -O0 -glldb     -fno-omit-frame-pointer     -ftemplate-backtrace-limit=40     -ftime-trace -pedantic -Wextra -Wsign-compare -Wstrict-aliasing -fstrict-aliasing -Wvexing-parse -Wfloat-conversion -fvisibility=hidden -fvisibility-inlines-hidden    -DDEBUG  -fdebug-prefix-map=/data/bdaci01/BioDataAnalysis/ci-tmp-U2404Sk64c191/Debug/boost=/home/bdaci01/builds/6b652442/0/BioDataAnalysis/boost    -DDEBUG  -march=skylake     -O0 -glldb     -fno-omit-frame-pointer     -ftemplate-backtrace-limit=40     -ftime-trace -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1   -I".." -I"/data/bdaci01/BioDataAnalysis/ci-dst-U2404Sk64c191/Debug/include" -I"/data/bdaci01/BioDataAnalysis/ci-dst-U2404Sk64c191/Extras/include" -I"/data/bdaci01/BioDataAnalysis/ci-tmp-U2404Sk64c191/Debug/boost"  -c -o "../bin.v2/libs/histogram/test/algorithm_empty_test.test/clang-linux-19/debug/x86_64/cxxstd-20-iso/link-static/optimization-speed/pch-off/threading-multi/algorithm_empty_test.o" "../libs/histogram/test/algorithm_empty_test.cpp"  
  
In file included from ../libs/histogram/test/algorithm_empty_test.cpp:14:  
In file included from ../libs/histogram/test/histogram.hpp:15:  
In file included from ../boost/histogram/make_histogram.hpp:17:  
In file included from ../boost/histogram/histogram.hpp:15:  
../boost/histogram/detail/fill.hpp:48:7: error: static assertion failed due to requirement 'std::is_convertible<std::tuple<int>, std::tuple<const double &>>::value': error: sample argument(s) not convertible to accumulator argument(s)  
   48 |       std::is_convertible<std::tuple<Passed...>, std::tuple<Expected...>>::value,  
      |       ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../boost/histogram/histogram.hpp:204:5: note: in instantiation of template class 'boost::histogram::detail::sample_args_passed_vs_expected<std::tuple<int>, std::tuple<const double &>>' requested here  
  204 |     detail::sample_args_passed_vs_expected<typename arg_traits::sargs,  
      |     ^  
../boost/histogram/histogram.hpp:193:12: note: in instantiation of function template specialization 'boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<>, boost::histogram::axis::integer<>>, boost::histogram::storage_adaptor<std::vector<boost::histogram::accumulators::weighted_mean<>>>>::operator()<const boost::histogram::weight_type<int> &, const int &, const int &, const boost::histogram::sample_type<std::tuple<int>> &>' requested here  
  193 |     return operator()(std::forward_as_tuple(arg0, args...));  
      |            ^  
../libs/histogram/test/algorithm_empty_test.cpp:46:5: note: in instantiation of function template specialization 'boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<>, boost::histogram::axis::integer<>>, boost::histogram::storage_adaptor<std::vector<boost::histogram::accumulators::weighted_mean<>>>>::operator()<boost::histogram::weight_type<int>, int, int, boost::histogram::sample_type<std::tuple<int>>, void>' requested here  
   46 |     h(weight(2), -2, -4, sample(3));  
      |     ^  
../libs/histogram/test/algorithm_empty_test.cpp:61:3: note: in instantiation of function template specialization 'run_tests<boost::histogram::static_tag>' requested here  
   61 |   run_tests<static_tag>();  
      |   ^  
1 error generated.  
```

---

## Comment 1

> Username: HDembinski  
> Created at: 2025-06-05 15:04:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/405#issuecomment-2944916838  

I can only install clang-19.1.1 on my machine (Ubuntu-24.04), where this problem doesn't seem to appear.  
  
@emmenlau There are lots of cmdline arguments in your call to clang++, some repetitive and redundant. Can you provide a minimal set of cmdline arguments for me to test?  
  
Combining -g with -O3 is somewhat contradictory, I don't know whether that's intentional.

---

## Comment 2

> Username: HDembinski  
> Created at: 2025-06-05 18:44:36 UTC  
> Updated at: 2025-06-05 18:51:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/405#issuecomment-2945665580  

The line which fails is `std::is_convertible<std::tuple<int>, std::tuple<const double &>>::value`, which actually looks like it *should* fail, because for the conversion int has to be converted into a temporary double, from which then the reference is taken. The conversion is possible but should lead to a potentially dangling reference. That being said, AFAIU `std::is_convertible` doesn't check whether potentially dangling references to intermediate temporaries are created, it just checks whether two types can be implicitly converted, and that is the case, `int x = 1; static_cast<const double&>(x);` is not a compiler error.  
  
When it comes to the nitty-gritty details of C++ law, I rather trust the empirical evidence. That same line apparently has been accepted by all compilers so far, starting with gcc-5.  
  
Final nail in the coffin. On Godbolt, the following code compiles with clang-20.1.0, so I assume that you stumbled over a compiler bug in clang-19.1.7.  
```cpp  
#include <type_traits>  
#include <tuple>  
  
int main() {  
    static_assert(std::is_convertible<std::tuple<int>, std::tuple<const double &>>::value, "");  
    return 0;  
}  
```  
  
I am closing the issue, but feel free to reopen if the problem does not disappear on compiler upgrade.
