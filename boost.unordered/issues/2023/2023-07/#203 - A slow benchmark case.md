# #203 - A slow benchmark case [Closed]

> Username: ktprime  
> Created at: 2023-07-31 06:56:35 UTC  
> Updated at: 2023-09-02 06:30:03 UTC  
> Closed at: 2023-09-02 06:30:03 UTC  
> Url: https://github.com/boostorg/unordered/issues/203  

add the following codes to [martinus's ](https://github.com/martinus/map_benchmark) benchmark,  this case is quite slow compared with the other hash map.  
  
```  
#include "Map.h"  
#include "bench.h"  
#include "hex.h"  
#include "sfc64.h"  
  
#include <algorithm>  
#include <sstream>  
  
BENCHMARK(RandomInsertEraseContinue) {  
    // random bits to set for the mask  
    size_t max_n = 100000;  
  
    using M = Map<uint32_t, uint32_t>;  
  
    for (int i = 0; i < 4; ++i) {  
#ifdef USE_POOL_ALLOCATOR  
        Resource<uint32_t, uint32_t> resource;  
        M map{0, M::hasher{}, M::key_equal{}, &resource};  
#else  
        M map;  
#endif  
  
        std::stringstream ss;  
        ss << (max_n / 1000000.) << " M cycles";  
        sfc64 rng(2023 + i);  
  
        // benchmark randomly inserting & erasing begin  
        bench.beginMeasure(ss.str().c_str());  
        for (size_t i = 0; i < max_n / 3; ++i)  
            map.emplace((uint32_t)rng(), 0);  
  
        auto key = map.begin()->first;  
        for (size_t i = max_n; i > 0; i--) {  
            auto it = map.find(key);  
            if (it == map.end()) {  
                it = map.begin(); key = it->first;  
            }  
  
            if constexpr(std::is_void_v<decltype(map.erase(it))>) {  
                map.erase(it);  
                if (++it != map.end()) key = it->first;  
            } else {  
                it = map.erase(it);  
                if (it != map.end()) key = it->first;  
            }  
  
            map.emplace((uint32_t)rng(), 0);  
        }  
  
        bench.endMeasure(map.size(), map.size());  
        max_n *= 7;  
    }  
}  
```  
  
/map_benchmark/build$ ./bench_ankerl_unordered_dense__std_hash RandomInsertEraseContinue  
"ankerl::unordered_dense::map"; "std::hash"; "RandomInsertEraseContinue"; "01"; "0.1 M cycles"; 33333; 0.0068379; 1.35938  
"ankerl::unordered_dense::map"; "std::hash"; "RandomInsertEraseContinue"; "02"; "0.7 M cycles"; 233282; 0.0290033; 6.13672  
"ankerl::unordered_dense::map"; "std::hash"; "RandomInsertEraseContinue"; "03"; "4.9 M cycles"; 1631192; 0.440561; 32.3633  
"ankerl::unordered_dense::map"; "std::hash"; "RandomInsertEraseContinue"; "04"; "34.3 M cycles"; 11327200; 4.35657; 256.363  
/map_benchmark/build$ ./bench_ktprime_hash_table7__std_hash RandomInsertEraseContinue  
"emhash7::HashMap"; "std::hash"; "RandomInsertEraseContinue"; "01"; "0.1 M cycles"; 33333; 0.0063198; 1.48047  
"emhash7::HashMap"; "std::hash"; "RandomInsertEraseContinue"; "02"; "0.7 M cycles"; 233290; 0.038941; 9.35938  
"emhash7::HashMap"; "std::hash"; "RandomInsertEraseContinue"; "03"; "4.9 M cycles"; 1631132; 0.351154; 36.3594  
"emhash7::HashMap"; "std::hash"; "RandomInsertEraseContinue"; "04"; "34.3 M cycles"; 11327292; 4.21666; 288.359  
/map_benchmark/build$ ./bench_boost_hashmap__std_hash RandomInsertEraseContinue  
"boost::flat_hash_map"; "std::hash"; "RandomInsertEraseContinue"; "01"; "0.1 M cycles"; 33333; 0.0086208; 1.42188  
"boost::flat_hash_map"; "std::hash"; "RandomInsertEraseContinue"; "02"; "0.7 M cycles"; 233299; 0.0408471; 8.86328  
"boost::flat_hash_map"; "std::hash"; "RandomInsertEraseContinue"; "03"; "4.9 M cycles"; 1631193; 7.71446; 34.3516  
"boost::flat_hash_map"; "std::hash"; "RandomInsertEraseContinue"; "04"; "34.3 M cycles"; 11327680; 106.978; 272.363  
/map_benchmark/build$ ./bench_jiwan_dense_hash_map__std_hash RandomInsertEraseContinue  
"jg::dense_hash_map"; "std::hash"; "RandomInsertEraseContinue"; "01"; "0.1 M cycles"; 33333; 0.0047614; 1.85938  
"jg::dense_hash_map"; "std::hash"; "RandomInsertEraseContinue"; "02"; "0.7 M cycles"; 233282; 0.0317114; 11.125  
"jg::dense_hash_map"; "std::hash"; "RandomInsertEraseContinue"; "03"; "4.9 M cycles"; 1631192; 0.279311; 61.5703  
"jg::dense_hash_map"; "std::hash"; "RandomInsertEraseContinue"; "04"; "34.3 M cycles"; 11327200; 2.77904; 384.363

---

## Comment 1

> Username: joaquintides  
> Created at: 2023-08-01 18:48:46 UTC  
> Url: https://github.com/boostorg/unordered/issues/203#issuecomment-1660894092  

Hi,   
  
We're analyzing this benchmark and will come up with some conclusions soon. I'd like to ask you: is this a synthetic benchmark you just invented, or is it inspired in some real-life use case you're familiar with?  
  
Thank you,

---

## Comment 2

> Username: ktprime  
> Created at: 2023-08-01 23:30:34 UTC  
> Updated at: 2023-08-02 04:31:58 UTC  
> Url: https://github.com/boostorg/unordered/issues/203#issuecomment-1661239359  

This case is a corner case happens when I test my ownern hash map emhash5, it can easy be reproduced.  
  
 It is an interesting case and many flat hash maps sufffers this performance issuse(including absl) , I  
collects many useful thirdparty hash map benchmark cases in my git emhash,   
one of them comes from [martin's](https://github.com/ktprime/emhash/blob/master/bench/martin_bench.cpp) and make some changes. boost flat hash map is the fastest one in most cases, but the last 3 case is not so.  
  
I have add this corner cases into my changed martin_bench. The following is how to reproduce it.  
all result is in [martin_bench.txt](https://github.com/ktprime/emhash/blob/master/bench/martin_bench.txt)  
  
```  
emhash/bench$  make BF=1 SW=1 std=20 CXX=clang++ ET=1  
g++ -flto=auto -O3 -DEMH_PSL=16 -DNDEBUG=1 -march=native -I.. -I../thirdparty  -DABSL_HMAP=1 -DHAVE_BOOST=1 -std=c++17 -static  martin_bench.cpp -o mbench  
g++ -flto=auto -O3 -DEMH_PSL=16 -DNDEBUG=1 -march=native -I.. -I../thirdparty  -DABSL_HMAP=1 -DHAVE_BOOST=1 -std=c++17 -static  ebench.cpp -o ebench  
g++ -flto=auto -O3 -DEMH_PSL=16 -DNDEBUG=1 -march=native -I.. -I../thirdparty  -DABSL_HMAP=1 -DHAVE_BOOST=1 -std=c++17 -static  buint64.cpp -o bi  
g++ -flto=auto -O3 -DEMH_PSL=16 -DNDEBUG=1 -march=native -I.. -I../thirdparty  -DABSL_HMAP=1 -DHAVE_BOOST=1 -std=c++17 -static  bstring.cpp -o bs  
g++ -flto=auto -O3 -DEMH_PSL=16 -DNDEBUG=1 -march=native -I.. -I../thirdparty  -DABSL_HMAP=1 -DHAVE_BOOST=1 -std=c++17 -static  word_count.cpp -o wc  
g++ -flto=auto -O3 -DEMH_PSL=16 -DNDEBUG=1 -march=native -I.. -I../thirdparty  -DABSL_HMAP=1 -DHAVE_BOOST=1 -std=c++17 -static  tbench.cpp -o tbench  
g++ -flto=auto -O3 -DEMH_PSL=16 -DNDEBUG=1 -march=native -I.. -I../thirdparty  -DABSL_HMAP=1 -DHAVE_BOOST=1 -std=c++17 -static  app.cpp -o app  
....  
```  
run benchmark start from no 11.   
emhash/bench$ ./mbench b11  
```  
------------------------------------------------------------------------------------------------------------  
clang 14.0.0  on llvm/gcc 4.2.1 __cplusplus = 202002 x86-64 OS = linux, cpu = 12th Gen Intel(R) Core(TM) i7-12700  
------------------------------------------------------------------------------------------------------------  
./test [2-9mptseb0d2 rjqf] n  
      absl            absl flat  
    ankerl         martin dense  
     boost           boost flat  
   emhash5              emhash5  
   emhash6              emhash6  
   emhash7              emhash7  
   emhash8              emhash8  
   emilib2              emilib2  
   emilib3              emilib3  
     hrd_m                 hrdm  
        jg             jg_dense  
     phmap           phmap flat  
   rigtorp              rigtorp  
robin_hood          martin flat  
cmd agrs = b11  
test hash: max_load_factor = 0.80  
      absl            absl flat  
    ankerl         martin dense  
     boost           boost flat  
   emhash5              emhash5  
   emhash6              emhash6  
   emhash7              emhash7  
   emhash8              emhash8  
   emilib2              emilib2  
   emilib3              emilib3  
     hrd_m                 hrdm  
        jg             jg_dense  
     phmap           phmap flat  
   rigtorp              rigtorp  
robin_hood          martin flat  
  
bench_AccidentallyQuadratic (10M int insert.copy.iterator):  
                     emhash6 time 1.06 s  
                     emhash7 time 0.92 s  
                     emhash5 time 1.23 s  
                     emhash8 time 1.62 s  
                    jg_dense time 1.70 s  
                     rigtorp time 1.36 s  
                martin dense time 1.59 s  
                  boost flat time 7.67 s  
                   absl flat time 3.09 s  
                        hrdm time 1.16 s  
                 martin flat time 0.92 s  
                  phmap flat time 3.61 s  
  
bench_InsertEraseContinue:  
    emhash6  
        1.00 333239 cycles time 0.03  
        3.00 999171 cycles time 0.16  
        9.00 2992752 cycles time 0.69  
        27.00 8934537 cycles time 2.05 total (2.95 s)  
    emhash7  
        1.00 333240 cycles time 0.03  
        3.00 999167 cycles time 0.11  
        9.00 2992650 cycles time 0.58  
        27.00 8934543 cycles time 1.70 total (2.44 s)  
    emhash5  
        1.00 333233 cycles time 0.03  
        3.00 999195 cycles time 0.16  
        9.00 2992682 cycles time 0.83  
        27.00 8934275 cycles time 2.38 total (3.41 s)  
    emhash8  
        1.00 333240 cycles time 0.05  
        3.00 999182 cycles time 0.12  
        9.00 2992802 cycles time 0.83  
        27.00 8934156 cycles time 2.67 total (3.69 s)  
    jg_dense  
        1.00 333240 cycles time 0.03  
        3.00 999182 cycles time 0.14  
        9.00 2992802 cycles time 0.61  
        27.00 8934156 cycles time 2.20 total (3.02 s)  
    martin dense  
        1.00 333240 cycles time 0.06  
        3.00 999182 cycles time 0.16  
        9.00 2992802 cycles time 0.97  
        27.00 8934156 cycles time 2.55 total (3.75 s)  
    boost flat  
        1.00 333245 cycles time 0.22  
        3.00 999146 cycles time 0.22  
        9.00 2992688 cycles time 14.88  
        27.00 8934480 cycles time 21.67 total (37.00 s)  
    absl flat  
        1.00 333242 cycles time 0.14  
        3.00 999192 cycles time 0.19  
        9.00 2992638 cycles time 6.45  
        27.00 8934489 cycles time 16.55 total (23.34 s)  
    martin flat  
        1.00 333241 cycles time 0.03  
        3.00 999189 cycles time 0.14  
        9.00 2992722 cycles time 0.66  
        27.00 8934362 cycles time 2.08 total (2.94 s)  
    phmap flat  
        1.00 333224 cycles time 0.03  
        3.00 999182 cycles time 0.20  
        9.00 2992641 cycles time 0.41  
        27.00 8934401 cycles time 6.69 total (7.36 s)  
  
bench_InsertEraseBegin:  
    emhash6  
        0.10 50000 cycles time 0.00  
        0.50 250000 cycles time 0.22  
        2.50 1250000 cycles time 2.44 total (2.66 s)  
    emhash7  
        0.10 50000 cycles time 0.00  
        0.50 250000 cycles time 0.20  
        2.50 1250000 cycles time 2.38 total (2.58 s)  
    emhash8  
        0.10 50000 cycles time 0.00  
        0.50 250000 cycles time 0.03  
        2.50 1250000 cycles time 0.16 total (0.19 s)  
    jg_dense  
        0.10 50000 cycles time 0.00  
        0.50 250000 cycles time 0.03  
        2.50 1250000 cycles time 0.14 total (0.17 s)  
    martin dense  
        0.10 50000 cycles time 0.00  
        0.50 250000 cycles time 0.03  
        2.50 1250000 cycles time 0.16 total (0.19 s)  
    boost flat  
        0.10 50000 cycles time 0.02  
        0.50 250000 cycles time 0.73  
        2.50 1250000 cycles time 9.02 total (9.77 s)  
    absl flat  
        0.10 50000 cycles time 0.08  
        0.50 250000 cycles time 11.28  
        2.50 1250000 cycles time 133.77 total (145.12 s)  
    martin flat  
        0.10 50000 cycles time 0.08  
        0.50 250000 cycles time 2.23  
        2.50 1250000 cycles time 55.47 total (57.78 s)  
    emhash5  
        0.10 50000 cycles time 0.03  
        0.50 250000 cycles time 4.42  
        2.50 1250000 cycles time 67.84 total (72.31 s)  
  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2023-08-02 08:36:15 UTC  
> Url: https://github.com/boostorg/unordered/issues/203#issuecomment-1661754464  

This deletion pattern (in iteration order) does indeed exhibit pathological behavior when combined with insertions, so we were wondering whether this is a case that actually occurs in practice.  
  
We tried to make the benchmark more realistic (and independent of iteration order, which varies by container) by changing it to erase the inserted numbers with a max_n/3 lag (by using another RNG for the erase, started from the same seed), but the pathological behavior doesn't occur.

---

## Comment 4

> Username: joaquintides  
> Created at: 2023-08-02 18:41:33 UTC  
> Url: https://github.com/boostorg/unordered/issues/203#issuecomment-1662762820  

As commented by @pdimov, we have tested your scenario and then another one that also keeps the size of the container constant but does not depend on iteration order --instead, values are erased a fixed offset behind insertion. This is the complete testing code:  
```cpp  
#include <algorithm>  
#include <array>  
#include <chrono>  
#include <numeric>  
  
std::chrono::high_resolution_clock::time_point measure_start,measure_pause;  
  
template<typename F>  
double measure(F f)  
{  
  using namespace std::chrono;  
  
  static const int              num_trials=1;;  
  static const milliseconds     min_time_per_trial(10);  
  std::array<double,num_trials> trials;  
  
  for(int i=0;i<num_trials;++i){  
    int                               runs=0;  
    high_resolution_clock::time_point t2;  
    volatile decltype(f())            res; /* to avoid optimizing f() away */  
  
    measure_start=high_resolution_clock::now();  
    do{  
      res=f();  
      ++runs;  
      t2=high_resolution_clock::now();  
    }while(t2-measure_start<min_time_per_trial);  
    trials[i]=duration_cast<duration<double>>(t2-measure_start).count()/runs;  
  }  
  
  std::sort(trials.begin(),trials.end());  
  return std::accumulate(  
    trials.begin(),trials.end(),0.0)/trials.size();  
}  
  
void pause_timing()  
{  
  measure_pause=std::chrono::high_resolution_clock::now();  
}  
  
void resume_timing()  
{  
  measure_start+=std::chrono::high_resolution_clock::now()-measure_pause;  
}  
  
#include <iostream>  
#include "absl/container/flat_hash_map.h"  
#include "absl/container/node_hash_map.h"  
#include <boost/core/detail/splitmix64.hpp>  
#include <boost/unordered/unordered_map.hpp>  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <boost/unordered/unordered_node_map.hpp>  
#include <unordered_map>  
  
struct splitmix64_urng:boost::detail::splitmix64  
{  
  using boost::detail::splitmix64::splitmix64;  
  using result_type=boost::uint64_t;  
  
  static constexpr result_type (min)(){return 0u;}  
  static constexpr result_type(max)()  
  {return (std::numeric_limits<result_type>::max)();}  
};  
  
template<typename Map>  
struct test_ktprime  
{  
  std::size_t operator()(std::size_t max_n,double reserve_factor)const  
  {  
    Map map;  
    splitmix64_urng rng(2023+max_n);  
  
    map.reserve((std::size_t)(max_n*reserve_factor));  
  
    pause_timing();  
    for(std::size_t i=0;i<max_n/3;++i){  
      map.emplace((std::uint32_t)rng(),0);  
    }  
    resume_timing();  
  
    auto key=map.begin()->first;  
    for(size_t i=0;i<max_n;++i){  
      auto it=map.find(key);  
      if(it==map.end()){  
        it=map.begin();  
        key=it->first;  
      }  
  
      if constexpr(std::is_void<decltype(map.erase(it))>::value){  
        map.erase(it++);  
        if(it!=map.end())key=it->first;  
      }  
      else{  
        it=map.erase(it);  
        if(it!=map.end())key=it->first;  
      }  
  
      map.emplace((std::uint32_t)rng(),0);  
    }  
    pause_timing();  
    auto res=map.size();  
    map.clear();  
    resume_timing();  
    return res;  
  }  
};  
  
template<typename Map>  
struct test_sliding  
{  
  std::size_t operator()(std::size_t max_n,double reserve_factor)const  
  {  
    Map map;  
    splitmix64_urng rng(2023+max_n),rnge(rng);  
  
    map.reserve((std::size_t)(max_n*reserve_factor));  
  
    pause_timing();  
    for(std::size_t i=0;i<max_n/3;++i){  
      map.emplace((std::uint32_t)rng(),0);  
    }  
    resume_timing();  
  
    for(size_t i=0;i<max_n;++i){  
      map.erase(rnge());  
      map.emplace((std::uint32_t)rng(),0);  
    }  
    pause_timing();  
    auto res=map.size();  
    map.clear();  
    resume_timing();  
    return res;  
  }  
};  
  
template<template<typename> class Test>  
void benchmark(const char* name)  
{  
  std::cout<<name<<std::endl;  
  for(double reserve_factor:{0.0,0.5,1.0}){  
    std::size_t max_n=100000;  
    using map_type1=std::unordered_map<std::uint32_t,std::uint32_t>;  
    using map_type2=boost::unordered_map<std::uint32_t,std::uint32_t>;  
    using map_type3=boost::unordered_flat_map<std::uint32_t,std::uint32_t>;  
    using map_type4=absl::flat_hash_map<std::uint32_t,std::uint32_t>;  
  
    std::cout<<"Reserve factor: "<<reserve_factor<<std::endl;  
    std::cout<<"cycles [M];std::unordered_map;boost::unordered_map;"  
                "boost::unordered_flat_map;absl::flat_hash_map"<<std::endl;  
  
    for(int i=0;i<10;++i){  
      auto t=measure(std::bind_front(Test<map_type1>(),max_n,reserve_factor));  
      std::cout<<max_n/1000000.<<";"<<t/max_n*1.E6<<";";  
  
      t=measure(std::bind_front(Test<map_type2>(),max_n,reserve_factor));  
      std::cout<<t/max_n*1.E6<<";";  
  
      t=measure(std::bind_front(Test<map_type3>(),max_n,reserve_factor));  
      std::cout<<t/max_n*1.E6<<";";  
  
      t=measure(std::bind_front(Test<map_type4>(),max_n,reserve_factor));  
      std::cout<<t/max_n*1.E6<<std::endl;  
  
      max_n*=2;  
    }  
  }  
}  
  
int main()  
{  
  benchmark<test_ktprime>("Insert randomly and erase from begin to end");  
  benchmark<test_sliding>("Insert randomly and erase some fixed offset behind");  
}  
  
#include "absl/container/internal/raw_hash_set.cc"  
#include "absl/hash/internal/hash.cc"  
#include "absl/hash/internal/low_level_hash.cc"  
#include "absl/hash/internal/city.cc"  
```  
And these are the results for the "sliding window" benchmark (VS 2022), where we don't observe any pathological behavior:  
![imagen](https://github.com/boostorg/unordered/assets/3283164/291c5fc6-31a0-4ac6-ba34-c5942ffd8fa5)

---

## Comment 5

> Username: ktprime  
> Created at: 2023-08-03 01:01:58 UTC  
> Updated at: 2023-08-03 07:23:49 UTC  
> Url: https://github.com/boostorg/unordered/issues/203#issuecomment-1663152968  

This case does not depend on the hash function and RNG generator.  
It's seems the code `map.reserve((std::size_t)(max_n*reserve_factor)) `works if I add it into my test code  
  
  during erasing by the iterator order the "sliding window" happens,  
there is a big blocks of **continuous hole/empty** buckets in memory and it moves back and forth.  
It's some difficulty for insertion to find an empty bucket because it's not a uniform distribution  
(**linear clustering** of empty bucket) and quadratic probing used in a fixed way (I use 2-3 probing ways in emhash).  
  
`ooooo_____o______oooo_oooooo_oooooooo_oooooo_o`  
  
   bucket distribution looks like the above("_" is empty, "o" is occupied)，"_____" means a continuous empty buckets generated after iterator erasion(very little 'o' in this block), the other part of occupied bucket becomes more and more **dense**.
