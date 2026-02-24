# #94 Add CMake Build System File Generator Support [Open]

> Username: coder3101  
> Created at: 2020-07-15 08:43:50 UTC  
> Updated at: 2021-09-09 12:45:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/94  

CMake is a widely used Build System Files Generator. This PR adds the support for CMake into Boost.uBLAS. As a result following things can be built with CMake  
  
- Examples  
- Benchmarks (Requires Boost.PROGRAM_OPTIONS)  
- Tensor Tests (Requires Boost.UNIT_TEST_FRAMEWORK)  
  
## Configuration Controls  
In order to build with CMake, you need to have either Boost installed or boost headers build from source using `b2 headers`.  You can pass an option to CMake `-DBOOST_HEADERS_DIR=<path_to_boost_headers>` and ublas will use those headers. If you skip this option, CMake will try to find a system installed Boost and use its include directories. If both of the above fails, CMake will fail and no build system files will be generated.  
  
### Control the targets to build  
  
By default, CMake will build system files for all of the above three (examples, test, benchmarks). If you want to quickly test something or want to build some specific targets. You can use the following flags:  
  
```bash  
$ cmake .. -DBUILD_TENSOR_TEST=OFF # It Will not build tensor unit tests  
$ cmake .. -DBUILD_EXAMPLES=ON # It Will build examples. (By default it will build all but if you disable any target once, you need to explicitly enable it again. We use cache to remember your last runs configurations)  
$ cmake .. -DBUILD_BENCHMARKS=OFF # It Will not build benchmarks, You will need to pass ON next time to again enable it.  
```  
  
### Build Tensor and Benchmarks  
`b2 headers` only collects the header files. This is not sufficient to build Tensor examples or Benchmarks as they need to be linked against Boost.Test Framework and Boost.ProgramOptions. Regardless of whether you use a custom Boost header directory or let cmake find it. You will need installed boost to link against those libraries.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-07-15 08:52:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/94#issuecomment-658638529  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/94?src=pr&el=h1) Report  
> Merging [#94](https://codecov.io/gh/boostorg/ublas/pull/94?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ublas/commit/7cc02bf0a10ad1820db83e5d4ed19b0510da2f1e&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/94/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/94?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #94   +/-   ##  
========================================  
  Coverage    95.33%   95.33%             
========================================  
  Files           22       22             
  Lines         1223     1223             
========================================  
  Hits          1166     1166             
  Misses          57       57             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/94?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/94?src=pr&el=footer). Last update [7cc02bf...e2d9ab0](https://codecov.io/gh/boostorg/ublas/pull/94?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
