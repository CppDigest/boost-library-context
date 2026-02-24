# #284 subrule: Fixed a huge use after scope bug [Merged]

> Username: Kojoley  
> Created at: 2017-11-20 21:26:58 UTC  
> Updated at: 2017-11-21 23:29:08 UTC  
> Merged at: 2017-11-21 23:26:43 UTC  
> Closed at: 2017-11-21 23:26:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/284  

It took me 3 days of pulling my hair to debug and fix the bug. The subrule's author put a big bomb under it and I caught the exposition only in a single test, with modern versions of Clang/GCC and enabled optimizations (most of boost regression test runners does not use `variant=release`). Valgrind did not catch any problems. Enabling address sanitizer causes the bug to disappear (now I understand why, it places big gaps between stack pointers). `-fsanitize-address-use-after-scope` catches the bug, but I have discovered that it is turned off in Clang by default after already having a repro.  
  
The bug could be easily reproduced if you use any parser that invalidates it's state in the destructor.  
  
I used `literal_char` with added `~literal_char() { ch = char_type(0); }`.  
  
Reproduction code:  
  
```cpp  
  
    rule<char const*> r;  
    subrule<0> entry;  
    r = (entry = 'a');  
    BOOST_TEST(test("a", r));  
```  
  
It will fail because after assignment to `r` a temporary `subrule_group` is destroyed and rule is binded to the already destroyed object.  
  
The cause is in usage of `reference` parser. I am 100% sure the code was copy-pasted from  `rule`/`grammar`. They store an expression within their body, while `subrule` actually used only as a placeholder.  
  
I have split `subrule_group` into an actual parser/generator that stores the expression and a Proto terminal that contains the parser/generator.  
  
Tested on:  
 - VS 2008, 2010, 2015, 2017  
 - Clang 3.8, 3.9, 4.0, 5.0  
 - GCC 4.8, 7

---

## Comment 1

> Username: Kojoley  
> Created_at: 2017-11-21 00:02:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/284#issuecomment-345872010  

I am going to ping the original `subrule`s author @frabar666. Maybe he can help in reviewing the PR.

---

## Comment 2

> Username: djowel  
> Created_at: 2017-11-21 07:10:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/284#issuecomment-345936863  

Oh my... Hmmm... do you think the subrule is still worth keeping? I wonder if anyone is using it. I don't.

---

## Comment 3

> Username: frabar666  
> Created_at: 2017-11-21 07:31:45 UTC  
> Updated_at: 2017-11-21 10:39:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/284#issuecomment-345940714  

Ouch... Indeed, much of the code was copied from grammar, and also from classic's subrule. I haven't worked with Spirit for years, and I'm afraid I don't remember enough about Spirit and Proto to give any useful feedback, sorry.  
  
I remember @djowel already mentioning years ago that the subrule mechanism was probably no longer useful in Qi/Karma (it was there in Classic for performance reasons, which no longer apply in Qi/Karma)... no objection here if you want to retire it.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2017-11-21 16:14:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/284#issuecomment-346077182  

I have made some benchmarks on VS 2017.  
  
`mini_xml2.cpp` vs `mini_xml2_sr.cpp`  
=====  
  
290Kb  
```  
--------------------------------------------------  
Benchmark           Time           CPU Iterations  
--------------------------------------------------  
BM_rule      12134563 ns   12535795 ns         56   22.0134M items/s  
BM_subrule   12367610 ns   11700075 ns         56   23.5858M items/s  
```  
  
114Mb xml  
---  
```  
--------------------------------------------------  
Benchmark           Time           CPU Iterations  
--------------------------------------------------  
BM_rule    9128121686 ns 9001257700 ns          1   12.6617M items/s  
BM_subrule 9123333133 ns 9032457900 ns          1   12.6179M items/s  
```  
  
It looks like subrules do not improve perfromance, but `mini_xml2`  heavily uses dynamic allocations and memory copy.  
  
------  
  
`start = (entry = *(a | b | c), a = 'a', b = 'b', c = 'c');`  
=====  
```  
--------------------------------------------------------  
Benchmark                 Time           CPU Iterations  
--------------------------------------------------------  
BM_rule/8               554 ns        558 ns    1258407   123.087M items/s rules  
BM_rule/64             2970 ns       2774 ns     213674   197.999M items/s rules  
BM_rule/512           22194 ns      22389 ns      32051   196.277M items/s rules  
BM_rule/4096         176862 ns     172102 ns       4079   204.276M items/s rules  
BM_rule/32768       1403790 ns    1375560 ns        499   204.462M items/s rules  
BM_rule/65536       2846764 ns    2836382 ns        264   198.316M items/s rules  
BM_rule_BigO           2.41 N       2.39 N  rules  
BM_rule_RMS               1 %          2 %  rules  
BM_subrule/8            298 ns        337 ns    2039614   204.033M items/s subrules  
BM_subrule/64          1051 ns       1064 ns     747858    516.35M items/s subrules  
BM_subrule/512         7063 ns       7092 ns     112179   619.622M items/s subrules  
BM_subrule/4096       55553 ns      55625 ns      11218    632.02M items/s subrules  
BM_subrule/32768     447610 ns     447664 ns       1603   628.262M items/s subrules  
BM_subrule/65536     893906 ns     875941 ns        748   642.166M items/s subrules  
BM_subrule_BigO        0.76 N       0.75 N  subrules  
BM_subrule_RMS            0 %          2 %  subrules  
```  
  
This simple test shows that rules have significant call overhead. So if you have a lot of `rule`s the performance boost from `subrule`s could be huge, especially if your parser just validates the input.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2017-11-21 16:40:31 UTC  
> Updated_at: 2017-11-21 16:43:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/284#issuecomment-346085915  

> I wonder if anyone is using it.  
  
Subrules were broken for about 2 years and never worked on c++11 (#259) so will not expect they are exist, but I think many people tried:  
  
 1) http://boost.2283326.n4.nabble.com/Spirit-grammar-for-IPv6-td2677293.html  
 2) http://spirit-general.narkive.com/HVmePKUw/repo-qi-a-subrule-re-implementation  
 3) http://boost.2283326.n4.nabble.com/boost-spirit-repository-qi-subrule-not-working-under-visual-studio-2008-td2682482.html  
 4) http://boost.2283326.n4.nabble.com/Repository-Qi-Strange-behavior-with-subrule-td3382265.html  
 5) https://svn.boost.org/trac10/ticket/5270  
  
I just found these links and can confirm the problems described in 4 and 5 before this patch, while with it they are fixed.

---

## Comment 6

> Username: djowel  
> Created_at: 2017-11-21 21:23:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/284#issuecomment-346165368  

Very thoughtful of you to do all these benchmarks! I wonder how X3 fares in comparison and I think for performance concerns such as call overhead, I'd go for X3 instead. The design of X3 is based on the subrules anyway.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2017-11-21 22:46:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/284#issuecomment-346186295  

> I wonder how X3 fares in comparison  
  
```c  
-----------------------------------------------------------  
Benchmark                    Time           CPU Iterations  
-----------------------------------------------------------  
BM_qi_rule/8               366 ns        350 ns    1869646   195.936M items/s qi_rule  
BM_qi_rule/64             2753 ns       2753 ns     249286   199.499M items/s qi_rule  
BM_qi_rule/512           21786 ns      21903 ns      32051   200.638M items/s qi_rule  
BM_qi_rule/4096         171558 ns     172102 ns       4079   204.276M items/s qi_rule  
BM_qi_rule/32768       1371057 ns    1344297 ns        499   209.217M items/s qi_rule  
BM_qi_rule/65536       2742350 ns    2756644 ns        249   204.052M items/s qi_rule  
BM_qi_rule_BigO           2.32 N       2.33 N  qi_rule  
BM_qi_rule_RMS               0 %          2 %  qi_rule  
BM_qi_subrule/8            105 ns        106 ns    7478585   645.438M items/s qi_subrule  
BM_qi_subrule/64           755 ns        765 ns     897430   718.196M items/s qi_subrule  
BM_qi_subrule/512         6001 ns       5980 ns     112179     734.9M items/s qi_subrule  
BM_qi_subrule/4096       47801 ns      47281 ns      11218   743.553M items/s qi_subrule  
BM_qi_subrule/32768     381998 ns     382398 ns       1795    735.49M items/s qi_subrule  
BM_qi_subrule/65536     761447 ns     765222 ns        897   735.081M items/s qi_subrule  
BM_qi_subrule_BigO        0.65 N       0.65 N  qi_subrule  
BM_qi_subrule_RMS            0 %          0 %  qi_subrule  
BM_x3/8                    100 ns        100 ns    7478585   685.778M items/s x3  
BM_x3/64                   748 ns        747 ns     897430   734.898M items/s x3  
BM_x3/512                 5941 ns       5980 ns     112179     734.9M items/s x3  
BM_x3/4096               47482 ns      47978 ns      14957    732.76M items/s x3  
BM_x3/32768             379852 ns     383746 ns       1870   732.907M items/s x3  
BM_x3/65536             760884 ns     765222 ns        897   735.081M items/s x3  
BM_x3_BigO                0.64 N       0.65 N  x3  
BM_x3_RMS                    0 %          0 %  x3  
```  
  
The numbers are different, because for previous benchmark I used `grammar` and because `x3` does not have it I have rewritten the benchmark to use just `rule`s.  
  
<details>  
  <summary>Benchmark code</summary>  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/include/qi.hpp>  
#include <boost/spirit/repository/include/qi_subrule.hpp>  
  
#include <benchmark/benchmark.h>  
  
#include <string>  
  
  
struct Helper  
{  
    Helper(benchmark::State & state_, std::string && label)  
      : state(state_)  
      , data()  
    {  
        for (auto i = 0; i < state.range(0); ++i)  
            data = data + "abcbcacab";  
  
        state.SetLabel(std::move(label));  
    }  
  
    ~Helper()  
    {  
        // complexity for "abc" in compare (`lit`) calls is  
        // a: 1, b: 2, c: 3; "abc" repeats 3 times  
        state.SetComplexityN(state.range(0) * (1 + 2 + 3) * 3);  
        state.SetItemsProcessed(state.iterations() * data.size());  
    }  
  
  
    benchmark::State & state;  
    std::string data;  
};  
  
static void BM_qi_subrule(benchmark::State & state)  
{  
    using boost::spirit::qi::rule;  
    using boost::spirit::repository::qi::subrule;  
    using boost::spirit::qi::parse;  
  
    Helper helper(state, "qi_subrule");  
    auto const iter = helper.data.begin();  
    auto const end = helper.data.end();  
    using Iterator = decltype(helper.data.end());  
  
    subrule<99> entry;  
    subrule<42> a;  
    subrule<48> b;  
    subrule<16> c;  
    rule<Iterator> start;  
  
    start = (  
        entry = *(a | b | c)  
      , a = 'a'  
      , b = 'b'  
      , c = 'c'  
    );  
  
    for (auto _ : state) {  
        benchmark::DoNotOptimize(parse(iter, end, start));  
    }  
}  
  
static void BM_qi_rule(benchmark::State & state)  
{  
    using boost::spirit::qi::rule;  
    using boost::spirit::qi::parse;  
  
    Helper helper(state, "qi_rule");  
    auto const iter = helper.data.begin();  
    auto const end = helper.data.end();  
    using Iterator = decltype(helper.data.end());  
  
    rule<Iterator> a, b, c, start;  
  
    a = 'a';  
    b = 'b';  
    c = 'c';  
  
    start = *(a | b | c);  
  
    for (auto _ : state) {  
        benchmark::DoNotOptimize(parse(iter, end, start));  
    }  
}  
  
static void BM_x3(benchmark::State & state)  
{  
    using boost::spirit::x3::lit;  
    using boost::spirit::x3::rule;  
    using boost::spirit::x3::parse;  
  
    auto a = lit('a');  
    auto b = lit('b');  
    auto c = lit('c');  
  
    rule<class r> r;  
    auto start =  
        r = *(a | b | c);  
  
    Helper helper(state, "x3");  
    auto const iter = helper.data.begin();  
    auto const end = helper.data.end();  
    using Iterator = decltype(helper.data.end());  
  
    for (auto _ : state) {  
        benchmark::DoNotOptimize(parse(iter, end, start));  
    }  
}  
  
#define MAKE_BENCH(name) \  
  BENCHMARK(name) \  
    /*->RangeMultiplier(2)*/ \  
    ->Range(8, 8 << 13) \  
    ->Complexity(benchmark::oN) \  
  /**/  
  
MAKE_BENCH(BM_qi_rule);  
MAKE_BENCH(BM_qi_subrule);  
MAKE_BENCH(BM_x3);  
  
BENCHMARK_MAIN()  
```  
  
Note: Code will note compile because of #257, I will open a PR for it on the week.  
  
</details>

---

## Comment 8

> Username: djowel  
> Created_at: 2017-11-21 22:59:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/284#issuecomment-346189292  

Very nice! You should add that in the spirit/workbench directory along with the other tests.

---

## Comment 9

> Username: Kojoley  
> Created_at: 2017-11-21 23:21:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/284#issuecomment-346193569  

> You should add that in the spirit/workbench directory along with the other tests.  
  
It uses [google benchmark](https://github.com/google/benchmark) library. Is this okay?  
  
And what about this PR? I think it is safe to merge because tests pass and even if somehow the PR introduces a regression no one will notice this because subrules are currently in a broken state (what PR should fix) and no-one uses them :smiley:.

---

## Comment 10

> Username: djowel  
> Created_at: 2017-11-21 23:27:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/284#issuecomment-346194683  

Hmmm.. probably not. Boost should not require any dependencies.

---
