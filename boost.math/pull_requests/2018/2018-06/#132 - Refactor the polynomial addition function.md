# #132 Refactor the polynomial addition function [Merged]

> Username: lakshayg  
> Created at: 2018-06-25 09:56:37 UTC  
> Updated at: 2018-06-27 06:39:01 UTC  
> Merged at: 2018-06-26 16:39:30 UTC  
> Closed at: 2018-06-26 16:39:30 UTC  
> Url: https://github.com/boostorg/math/pull/132  

* Remove in-loop calls to push_back thus eliminating the  
  possibility of multiple reallocations of memory to the  
  underlying vector.  
* Remove redundant `sign` parameter from the addition method  
* Delete the unused `detail::identity` struct

---

## Comment 1

> Username: NAThompson  
> Created_at: 2018-06-26 02:35:32 UTC  
> Url: https://github.com/boostorg/math/pull/132#issuecomment-400158409  

Could you write a quick google benchmark to demo that the pushback removal in fact does improve performance? Just paste it in the comments and the timing you got on your machine and then I'll run it on mine to verify.

---

## Comment 2

> Username: lakshayg  
> Created_at: 2018-06-26 05:29:33 UTC  
> Updated_at: 2018-06-26 05:29:54 UTC  
> Url: https://github.com/boostorg/math/pull/132#issuecomment-400183485  

Here are the results of the benchmark run on a Late 2013 MacBook Pro  
Processor: 2.6 GHz Intel Core i7  
Memory: 16 GB 1600 MHz DDR3  
  
```  
➜  math git:(addition) ✗ g++ -I include -O3 --std=c++14 test.cpp -lbenchmark && ./a.out  
2018-06-25 22:14:42  
Running ./a.out  
Run on (8 X 2600 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 6291K (x1)  
-------------------------------------------------------------------  
Benchmark                            Time           CPU Iterations  
-------------------------------------------------------------------  
BM_polynomial_addition/1000     226227 ns     225834 ns       3101  
BM_polynomial_addition/2000     266768 ns     265036 ns       2698  
BM_polynomial_addition/3000     308904 ns     305991 ns       2378  
BM_polynomial_addition/4000     323821 ns     322033 ns       2123  
BM_polynomial_addition/5000     353729 ns     352041 ns       1955  
BM_polynomial_addition/6000     395917 ns     394010 ns       1778  
BM_polynomial_addition/7000     431292 ns     429304 ns       1626  
➜  math git:(addition) ✗ git checkout master  
Branch master set up to track remote branch master from origin.  
Switched to a new branch 'master'  
➜  math git:(master) ✗ g++ -I include -O3 --std=c++14 test.cpp -lbenchmark && ./a.out  
2018-06-25 22:20:36  
Running ./a.out  
Run on (8 X 2600 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 6291K (x1)  
-------------------------------------------------------------------  
Benchmark                            Time           CPU Iterations  
-------------------------------------------------------------------  
BM_polynomial_addition/1000     489148 ns     488658 ns       1433  
BM_polynomial_addition/2000     708632 ns     707512 ns        985  
BM_polynomial_addition/3000     920136 ns     919329 ns        761  
BM_polynomial_addition/4000    1100081 ns    1099058 ns        635  
BM_polynomial_addition/5000    1335779 ns    1333942 ns        520  
BM_polynomial_addition/6000    1537176 ns    1534570 ns        461  
BM_polynomial_addition/7000    1700556 ns    1698215 ns        410  
```  
  
The first table shows the numbers after this patch is incorporated and the ones below one are using the master branch.  
  
Here is the code I used for benchmarking  
  
```cpp  
#include "boost/math/tools/polynomial.hpp"  
#include <benchmark/benchmark.h>  
#include <random>  
  
using namespace boost::math::tools; // for polynomial  
  
std::random_device rd;  
std::mt19937 rng(rd());  
std::uniform_int_distribution<int> uni(-(1u<<10), 1u<<10);  
  
polynomial<int> get_polynomial(int len)  
{  
    std::vector<int> v;  
    for (auto i = 0; i < len; ++i)  
        v.push_back(uni(rng));  
    return polynomial<int>{v.begin(), v.end()};  
}  
  
  
static void BM_polynomial_addition(benchmark::State& state)  
{  
    for (auto _ : state) {  
        for (int i = 0; i < 100; ++i) {  
            state.PauseTiming();  
            // the reason for keeping a very small compared to b is  
            // to cause multiple reallocations of the vector during addition  
            // and demonstrate the cases in which this patch will help  
            polynomial<int> a = get_polynomial(1);  
            polynomial<int> b = get_polynomial(state.range(0));  
            state.ResumeTiming();  
            benchmark::DoNotOptimize(a += b);  
        }  
    }  
}  
  
BENCHMARK(BM_polynomial_addition)  
    ->Arg(1000)  
    ->Arg(2000)  
    ->Arg(3000)  
    ->Arg(4000)  
    ->Arg(5000)  
    ->Arg(6000)  
    ->Arg(7000);  
  
BENCHMARK_MAIN();  
```

---

## Comment 3

> Username: NAThompson  
> Created_at: 2018-06-26 07:07:16 UTC  
> Url: https://github.com/boostorg/math/pull/132#issuecomment-400202709  

I have confirmed your speedups on my machine; furthermore I've run the PR under all the sanitizers provided by Apple Clang, and the code is clean before and after the changes have been applied. The code is also clean under cppcheck.  
  
However, your benchmark seems to be primarily testing the efficiency of the `PauseTIming()`/`ResumeTiming()` routine. I have made the following changes to your benchmark:  
  
```  
static void BM_polynomial_addition(benchmark::State& state)  
{  
    polynomial<int> a = get_polynomial(1);  
    polynomial<int> b = get_polynomial(state.range(0));  
    for (auto _ : state)  
    {  
            benchmark::DoNotOptimize(a += b);  
    }  
}  
  
BENCHMARK(BM_polynomial_addition)  
    ->Arg(1000)  
    ->Arg(2000)  
    ->Arg(3000)  
    ->Arg(4000)  
    ->Arg(5000)  
    ->Arg(6000)  
    ->Arg(7000)  
    ->Arg(8000)  
    ->Arg(9000)  
    ->Arg(10000);  
```  
  
and obtain the following results:  
  
```  
➜  math git:(develop) ✗ rm -f a.out; g++ --std=c++14 -O3 test.cpp -I./include  -lbenchmark && ./a.out  
2018-06-26 15:02:43  
Run on (4 X 2700 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x2)  
  L1 Instruction 32K (x2)  
  L2 Unified 262K (x2)  
  L3 Unified 3145K (x1)  
--------------------------------------------------------------------  
Benchmark                             Time           CPU Iterations  
--------------------------------------------------------------------  
BM_polynomial_addition/1000         129 ns        129 ns    4600783  
BM_polynomial_addition/2000         252 ns        252 ns    2762649  
BM_polynomial_addition/3000         374 ns        373 ns    1860801  
BM_polynomial_addition/4000         531 ns        522 ns    1224076  
BM_polynomial_addition/5000         934 ns        917 ns     752397  
BM_polynomial_addition/6000        1082 ns       1078 ns     635462  
BM_polynomial_addition/7000        1267 ns       1265 ns     544883  
BM_polynomial_addition/8000        1441 ns       1440 ns     478727  
BM_polynomial_addition/9000        1612 ns       1612 ns     420719  
BM_polynomial_addition/10000       1791 ns       1789 ns     383219  
```  
  
and  
  
```  
➜  math git:(addition) ✗ rm -f a.out; g++ --std=c++14 -O3 test.cpp -I./include  -lbenchmark && ./a.out  
2018-06-26 14:59:44  
Run on (4 X 2700 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x2)  
  L1 Instruction 32K (x2)  
  L2 Unified 262K (x2)  
  L3 Unified 3145K (x1)  
--------------------------------------------------------------------  
Benchmark                             Time           CPU Iterations  
--------------------------------------------------------------------  
BM_polynomial_addition/1000         131 ns        130 ns    4666356  
BM_polynomial_addition/2000         252 ns        252 ns    2753369  
BM_polynomial_addition/3000         374 ns        373 ns    1827380  
BM_polynomial_addition/4000         496 ns        495 ns    1320580  
BM_polynomial_addition/5000         822 ns        821 ns     763634  
BM_polynomial_addition/6000        1073 ns       1073 ns     638669  
BM_polynomial_addition/7000        1252 ns       1249 ns     546099  
BM_polynomial_addition/8000        1416 ns       1415 ns     480958  
BM_polynomial_addition/9000        1610 ns       1610 ns     416092  
BM_polynomial_addition/10000       1809 ns       1806 ns     382075  
```  
  
Is appears that your change is faster in 6 circumstances, and slower in 2, which is not a really unambiguous win. Has the changes to your benchmark changed the meaning and intended use case of your PR?

---

## Comment 4

> Username: lakshayg  
> Created_at: 2018-06-26 07:19:21 UTC  
> Updated_at: 2018-06-26 07:30:29 UTC  
> Url: https://github.com/boostorg/math/pull/132#issuecomment-400205805  

```  
static void BM_polynomial_addition(benchmark::State& state)  
{  
    polynomial<int> a = get_polynomial(1);  
    polynomial<int> b = get_polynomial(state.range(0));  
    for (auto _ : state)  
    {  
            benchmark::DoNotOptimize(a += b); // after the 1st execution, a becomes as large as b  
    }  
}  
```  
  
Moving the calls to `get_polynomial` outside the benchmark loop changes the semantics. The idea of this PR was to optimize the case when a large polynomial is added to a smaller one. After the change that you have made, once the first `a += b` executes, `a` becomes the same size as `b` and the benchmark no longer tests what it was supposed to.  
  
The slight performance difference that you observe in the modified benchmark is probably just experimental noise. I would expect the results for this benchmark to look identical for both the implementations of addition.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2018-06-26 11:19:28 UTC  
> Url: https://github.com/boostorg/math/pull/132#issuecomment-400271938  

Ok, got it. So I made the following change to your benchmark:  
  
```  
static void BM_polynomial_addition(benchmark::State& state)  
{  
    polynomial<int> b = get_polynomial(state.range(0));  
    for (auto _ : state)  
    {  
        polynomial<int> a = get_polynomial(1);  
        benchmark::DoNotOptimize(a += b);  
    }  
}  
```  
  
which I think captures your intent. The performance difference is dramatic:  
  
```  
➜  math git:(addition) ✗ rm -f a.out&& g++ -I./include --std=c++14 -O3 test.cpp -lbenchmark && ./a.out  
2018-06-26 19:15:19  
Run on (4 X 2700 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x2)  
  L1 Instruction 32K (x2)  
  L2 Unified 262K (x2)  
  L3 Unified 3145K (x1)  
---------------------------------------------------------------------  
Benchmark                              Time           CPU Iterations  
---------------------------------------------------------------------  
BM_polynomial_addition/5             425 ns        424 ns    1633813  
BM_polynomial_addition/10            483 ns        483 ns    1210319  
BM_polynomial_addition/20            485 ns        471 ns    1581160  
BM_polynomial_addition/30            479 ns        478 ns    1351064  
BM_polynomial_addition/50            440 ns        439 ns    1514457  
BM_polynomial_addition/80            432 ns        432 ns    1613851  
BM_polynomial_addition/100           442 ns        440 ns    1639490  
BM_polynomial_addition/200           462 ns        461 ns    1514299  
BM_polynomial_addition/500           474 ns        474 ns    1488136  
BM_polynomial_addition/1000          609 ns        609 ns    1131825  
BM_polynomial_addition/2000          860 ns        860 ns     799388  
BM_polynomial_addition/3000         1118 ns       1117 ns     617235  
BM_polynomial_addition/4000         1388 ns       1387 ns     497795  
BM_polynomial_addition/5000         1875 ns       1872 ns     385187  
BM_polynomial_addition/6000         2168 ns       2167 ns     313937  
BM_polynomial_addition/7000         2656 ns       2655 ns     263821  
BM_polynomial_addition/8000         3066 ns       3064 ns     224066  
BM_polynomial_addition/9000         3495 ns       3494 ns     200308  
BM_polynomial_addition/10000        3831 ns       3828 ns     182347  
BM_polynomial_addition/20000        6783 ns       6778 ns     100430  
BM_polynomial_addition/30000       10024 ns      10021 ns      60878  
BM_polynomial_addition/40000       17450 ns      17442 ns      38982  
BM_polynomial_addition/100000      51966 ns      51925 ns      12320  
BM_polynomial_addition/500000     326063 ns     325130 ns       2170  
➜  math git:(addition) ✗ git checkout develop  
Switched to branch 'develop'  
Your branch is up-to-date with 'origin/develop'.  
➜  math git:(develop) ✗ rm -f a.out&& g++ -I./include --std=c++14 -O3 test.cpp -lbenchmark && ./a.out  
2018-06-26 19:15:53  
Run on (4 X 2700 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x2)  
  L1 Instruction 32K (x2)  
  L2 Unified 262K (x2)  
  L3 Unified 3145K (x1)  
---------------------------------------------------------------------  
Benchmark                              Time           CPU Iterations  
---------------------------------------------------------------------  
BM_polynomial_addition/5             613 ns        612 ns    1173589  
BM_polynomial_addition/10            808 ns        807 ns     849092  
BM_polynomial_addition/20            979 ns        978 ns     706215  
BM_polynomial_addition/30           1004 ns       1003 ns     683587  
BM_polynomial_addition/50           1186 ns       1184 ns     577105  
BM_polynomial_addition/80           1426 ns       1425 ns     506553  
BM_polynomial_addition/100          1459 ns       1458 ns     465391  
BM_polynomial_addition/200          1746 ns       1745 ns     381232  
BM_polynomial_addition/500          2551 ns       2549 ns     275350  
BM_polynomial_addition/1000         3892 ns       3890 ns     176725  
BM_polynomial_addition/2000         6477 ns       6467 ns     103373  
BM_polynomial_addition/3000         8879 ns       8874 ns      78055  
BM_polynomial_addition/4000        10985 ns      10979 ns      61920  
BM_polynomial_addition/5000        13868 ns      13864 ns      49594  
BM_polynomial_addition/6000        16047 ns      16043 ns      42798  
BM_polynomial_addition/7000        19329 ns      19108 ns      37806  
BM_polynomial_addition/8000        21957 ns      21562 ns      29621  
BM_polynomial_addition/9000        24992 ns      24755 ns      27847  
BM_polynomial_addition/10000       28549 ns      28072 ns      25066  
BM_polynomial_addition/20000       55155 ns      54695 ns      12339  
BM_polynomial_addition/30000       81175 ns      80300 ns       8793  
BM_polynomial_addition/40000      113200 ns     111654 ns       6379  
BM_polynomial_addition/100000     270671 ns     268754 ns       2280  
BM_polynomial_addition/500000    1458991 ns    1435832 ns        505  
```  
  
John will need to sign off before it can be merged, but at this point I'm satisfied.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2018-06-26 16:39:26 UTC  
> Url: https://github.com/boostorg/math/pull/132#issuecomment-400382129  

Looks good to me too, merging...

---
