# #57 - Proposal: Faster RANLUX PRNGs [Open]

> Username: christoph-conrads  
> Created at: 2019-09-26 09:06:10 UTC  
> Updated at: 2024-03-01 15:33:19 UTC  
> Url: https://github.com/boostorg/random/issues/57  

This is a a cross-post from the boost developers mailing list from App 10.  
  
Boost.Random and the C++11 standard library ship with a 24- and a 48-bit  
RANLUX pseudo-random number generator (PRNG). I propose to add 32- and  
64-bit RANLUX PRNGs because they are at least 40% faster. Additionally,  
the 32-bit RANLUX PRNG can be implemented as follows:  
```c++  
  #include <boost/cstdint.hpp>  
  #include <boost/random.hpp>  
  
  namespace br = boost::random;  
  
  typedef  
    br::subtract_with_carry_engine<boost::uint32_t,32,3,17>  
    ranlux32_base;  
  typedef  
    br::discard_block_engine<ranlux32_base, 389, 16>  
    ranlux32;  
```  
There is C++98 benchmark code at the end of this e-mail requiring only  
Boost and a C++ compiler.  
  
Below I will briefly discuss the theory behind RANLUX PRNGs, where the  
new generators are coming from, and why the 64-bit RANLUX PRNG requires  
(a few lines of) new code.  
  
Drawing from RANLUX PRNGs is a two-step process. In the first step a  
value is drawn from a "subtract-with-borrow" (SWB) generator  
implementing b-bit modular arithmetic [1]. Then, values are generously  
discarded yielding uniformly distributed values (uniform in a dynamical  
systems model) [2]. The RANLUX PRNGs using 24- and 48-bit arithmetic  
always gave me an itch so I looked into the motivation behind this. It  
turns out [3] that this restriction arose likely from a) computational  
limitations in the 1990s and b) LÃ¼scher's interest in the generation of  
floating-point numbers (single-precision floating-point numbers have  
24-bit significand).  
  
SWB PRNGs come in two flavors (Type I, Type II) and they make use of  
three parameters called base b, short lag s, and long lag r. Using  
modern prime factorization methods, one can quickly come up with the  
32-bit PRNG above, a Type I SWB generator with b=2^32, r=17, and s=3.  
This one can be implemented as shown above.  
  
To generate 64-bit values, one should use a Type II SWB with b=2^64,  
r=62, and s=5 (all other 64-bit generators with b=2^64 and r < 100 will  
be awfully slow). What distinguishes the different types of SWBs is the  
recurrence for the computation of variates. For Type I it is  
  
x_n := (x_{n-s} - x_{n-r} + c_n) mod b,  
  
where c_n is a carry bit. For Type II it is  
  
x_n := (x_{n-r} - x_{n-s} + c_n) mod b.  
  
(Observe the order of long lang and short lag in these formulas.) Type  
II SWBs are not difficult to implement but it requires either a  
modification of `subtract_with_carry_engine` or a new class.  
  
Let me know what you think about this proposal.  
  
Christoph Conrads  
  
[1] G. Marsaglia, A. Zaman: "A New Class of Random Number Generators".  
    1991. URL: https://www.jstor.org/stable/2959748  
[2] Martin Lüscher: "A Portable High-Quality Random Number Generator for  
    Lattice Field Theory Simulations". 1994. URL:  
    https://arxiv.org/abs/hep-lat/9309020  
[3] Christoph Conrads: "Faster RANLUX Pseudo-Random Number Generators".  
    2019. URL:  
    https://christoph-conrads.name/faster-ranlux-pseudo-random-number-generators  
  
```c++  
// Build with:  
// c++ -Wextra -Wall -std=c++98 -pedantic benchmark.cpp -O3 \  
// -march=native -DNDEBUG -lboost_chrono -lboost_system  
#include <boost/chrono.hpp>  
#include <boost/cstdint.hpp>  
#include <boost/random.hpp>  
#include <cassert>  
#include <cstdio>  
#include <limits>  
#include <string>  
  
namespace chrono = boost::chrono;  
  
typedef chrono::milliseconds milliseconds;  
typedef chrono::process_cpu_clock my_clock;  
  
namespace br = boost::random;  
  
typedef  
    br::subtract_with_carry_engine<boost::uint32_t,32,3,17>  
    ranlux32_base;  
typedef br::discard_block_engine<ranlux32_base, 389, 16> ranlux32;  
  
struct dummy_engine  
{  
  typedef boost::uint32_t result_type;  
  
  static result_type max()  
  { return std::numeric_limits<result_type>::max(); }  
  static result_type min()  
  { return std::numeric_limits<result_type>::min(); }  
  
  result_type operator() ();  
  
  result_type state_;  
};  
  
dummy_engine::result_type dummy_engine::operator() ()  
{  
    return state_++;  
}  
  
std::string get_name(const dummy_engine&) { return "dummy-prng"; }  
std::string get_name(const br::ranlux24&) { return "ranlux24"; }  
std::string get_name(const br::ranlux48&) { return "ranlux48"; }  
std::string get_name(const ranlux32&) { return "ranlux32";}  
  
template<typename Generator>  
typename Generator::result_type  
draw(Generator& gen) __attribute__((noinline));  
  
template<typename Generator>  
typename Generator::result_type draw(Generator& gen)  
{  
  return gen();  
}  
  
template<typename Generator>  
void run(boost::uintmax_t num_draws)  
{  
  assert(Generator::min() == 0);  
  
  unsigned w =  
    Generator::max() == std::numeric_limits<boost::uint64_t>::max()?8u:  
    Generator::max() == (UINTMAX_C(1)<<48) - 1u ?6u:  
    Generator::max() == std::numeric_limits<boost::uint32_t>::max()?4u:  
    Generator::max() == (UINTMAX_C(1)<<24) - 1u ?3u:  
    Generator::max() == std::numeric_limits<boost::uint16_t>::max()?2u:  
    Generator::max() == std::numeric_limits<boost::uint8_t>::max() ?1u:  
    0u  
  ;  
  
  Generator gen;  
  my_clock::time_point t_0 = my_clock::now();  
  
  for(uintmax_t i = 0; i < num_draws; ++i)  
    draw(gen);  
  
  my_clock::time_point t_1 = my_clock::now();  
  milliseconds t_ms = chrono::duration_cast<milliseconds>(t_1 - t_0);  
  double bytes_per_sec = w * num_draws * 1000u/double(t_ms.count());  
  std::string name = get_name(gen);  
  boost::uint16_t dummy = boost::uint16_t(gen());  
  
  std::printf(  
    "%-26s | %10lu | %20.2e | %hu\n",  
    name.c_str(), t_ms.count(), bytes_per_sec, dummy  
  );  
}  
  
int main()  
{  
  boost::uintmax_t num_draws = UINTMAX_C(100000000);  
  
  run<dummy_engine>(num_draws);  
  run<br::ranlux24>(num_draws);  
  run<ranlux32>(num_draws);  
  run<br::ranlux48>(num_draws);  
}  
```

---

## Comment 1

> Username: kotika  
> Created at: 2019-09-26 10:10:41 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-535436525  

> On Sep 26, 2019, at 12:06, Christoph Conrads <notifications@github.com> wrote:  
>   
> This is a a cross-post from the boost developers mailing list from App 10.  
>   
> Boost.Random and the C++11 standard library ship with a 24- and a 48-bit  
> RANLUX pseudo-random number generator (PRNG). I propose to add 32- and  
> 64-bit RANLUX PRNGs because they are at least 40% faster. Additionally,  
> the 32-bit RANLUX PRNG can be implemented as follows:  
> ....  
> typedef br::subtract_with_carry_engine<boost::uint32_t,32,3,17>  ranlux32_base;  
>     
> typedef  br::discard_block_engine<ranlux32_base, 389, 16>    ranlux32;  
>   
  
I see three separate issues with your proposal.  
  
1) It is very well established that  subtract_with_carry + discard_block_engine = RANLUX  
only for the very specific parameter sets described in the paper by Luscher. What you have is not RANLUX.  
I dont think it is fair to use open-source software names   
without permission. I guess you could rename it to address this concern.  
  
2) You say it is 40% faster than RANLUX, but RANLUX itself is 10-20 times slower that the other generators.  
As a result, your generators would still be 5-10 times slower than the others.   
  
3) Your paper is not published in a journal.  I know, it is a little old fashioned in the age of facebook, but it is still a requirement.  
  
Regards, Kostas  
  
========================================  
  
Institute of Nuclear and Particle Physics  
NCSR Demokritos, Athens, Greece  
  
http://mixmax.hepforge.org  
https://github.com/kotika/random/  
  
========================================

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-09-26 12:02:36 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-535471486  

> 3) Your paper is not published in a journal.  I know, it is a little old fashioned in the age of facebook, but it is still a requirement.  
  
And we're talking about standardizing PCG! I think that ship has sailed.  
  
@christoph-conrads: Could you post code to run it through PractRand as well as your results?

---

## Comment 3

> Username: swatanabe  
> Created at: 2019-09-26 12:36:27 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-535482431  

AMDG  
  
On 9/26/19 6:02 AM, Nick wrote:  
>> 3) Your paper is not published in a journal.  I know, it is a little old fashioned in the age of facebook, but it is still a requirement.  
>   
> And we're talking about standardizing PCG! I think that ship has sailed.  
>   
  
I don't have the expertise to evaluate the quality of  
a PRNG algorithm.  I'm not convinced that the standards  
committee does either.  
  
> @christoph-conrads: Could you post code to run it through PractRand as well as your results?  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: kotika  
> Created at: 2019-09-26 12:46:04 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-535485741  

> On Sep 26, 2019, at 15:02, Nick <notifications@github.com> wrote:  
> And we're talking about standardizing PCG! I think that ship has sailed.  
>   
  
This one is slightly better than PCG, but both are far inferior to MT64. Every other generator in boost and the standard are  
completely obsolete.  
  
Cheers,  
Kostas

---

## Comment 5

> Username: NAThompson  
> Created at: 2019-09-26 13:06:14 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-535493361  

> I don't have the expertise to evaluate the quality of a PRNG algorithm.  
  
You don't need to; if the code looks good to you, I'll do the requisite evaluation.

---

## Comment 6

> Username: christoph-conrads  
> Created at: 2019-09-28 08:00:43 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536164110  

> @christoph-conrads: Could you post code to run it through PractRand as well as your results?  
  
  
Compile with `g++ -Wextra -Wall -std=c++98 -pedantic -O3 -march=native -DNDEBUG print-random.cpp -o print-random`. The program below prints raw random data to stdout. Run as follows:  
* dieharder: `./print-random | stdbuf --output=L time -p dieharder -a -g 200 -Y 1 -k 2 | tee dieharder.log`  
* practrand: `./print-random | stdbuf --output=L /tmp/pr/PractRand_094/RNG_test stdin64 -multithreaded -a -tlmin 64G | tee --append practrand.txt`  
  
```c++  
// This program copies values drawn from the new RANLUX variants to the standard  
// output file descriptor. This data can then be piped into other program, e.g.,  
// dieharder:  
// ./print-random | dieharder -a -g 200 -Y 1 -k 2  
#include <boost/cstdint.hpp>  
#include <boost/random.hpp>  
#include <cassert>  
#include <cstdio>  
#include <cstdlib>  
#include <limits>  
#include <unistd.h>  
#include <vector>  
  
namespace br = boost::random;  
  
typedef  
    br::subtract_with_carry_engine<boost::uint32_t,32,3,17>  
    ranlux32_base;  
typedef  
    br::discard_block_engine<ranlux32_base, 389, 16>  
    ranlux32;  
  
void print()  
{  
    typedef ranlux32 Generator;  
    typedef typename Generator::result_type T;  
  
    assert(std::numeric_limits<T>::max() == Generator::max());  
    assert(std::numeric_limits<T>::min() == Generator::min());  
  
    const std::size_t N = (std::size_t(1) << 20) / sizeof(T);  
    Generator gen;  
    std::vector<T> xs(N);  
  
    while(true)  
    {  
        for(std::size_t i = 0; i < xs.size(); ++i)  
            xs[i] = gen();  
  
        const std::size_t num_bytes = N * sizeof(T);  
        ssize_t ret = write(STDOUT_FILENO, xs.data(), num_bytes);  
  
        if(ret < 0)  
        {  
            std::fprintf(stderr, "write (ret=%zd)", ret);  
            std::exit(1);  
        }  
  
        std::size_t retu = static_cast<std::size_t>(ret);  
  
        if(retu != num_bytes)  
        {  
            std::fprintf(  
                stderr, "write (ret=%zu, num-bytes=%zu)", retu, num_bytes  
            );  
            std::exit(1);  
        }  
    }  
}  
  
int main()  
{  
    print();  
}  
```  
Below you find for the 32-bit generator  
* Practrand results (64G, 128G),  
* dieharder results,  
* practrand build instructions because v0.94 does not work out of the box on Linux (see the linked items).  
  
Let me know if you anything else.  
  
```  
RNG_test using PractRand version 0.94  
RNG = RNG_stdin64, seed = unknown  
test set = core, folding = standard (64 bit)  
  
rng=RNG_stdin64, seed=unknown  
length= 64 gigabytes (2^36 bytes), time= 599 seconds  
  Test Name                         Raw       Processed     Evaluation  
  BCFN(2+0,13-0,T)                  R=  +1.8  p = 0.226     normal             
  BCFN(2+1,13-0,T)                  R=  -0.2  p = 0.535     normal             
  BCFN(2+2,13-0,T)                  R=  +1.7  p = 0.240     normal             
  BCFN(2+3,13-0,T)                  R=  +1.5  p = 0.267     normal             
  BCFN(2+4,13-0,T)                  R=  -0.5  p = 0.583     normal             
  BCFN(2+5,13-0,T)                  R=  +2.8  p = 0.125     normal             
  BCFN(2+6,13-0,T)                  R=  -1.5  p = 0.724     normal             
  BCFN(2+7,13-0,T)                  R=  -2.8  p = 0.872     normal             
  BCFN(2+8,13-1,T)                  R=  +1.3  p = 0.288     normal             
  BCFN(2+9,13-1,T)                  R=  +1.6  p = 0.250     normal             
  BCFN(2+10,13-2,T)                 R=  +1.2  p = 0.307     normal             
  BCFN(2+11,13-3,T)                 R=  +1.1  p = 0.312     normal             
  BCFN(2+12,13-3,T)                 R=  +1.1  p = 0.315     normal             
  BCFN(2+13,13-4,T)                 R=  -1.6  p = 0.742     normal             
  BCFN(2+14,13-5,T)                 R=  +5.2  p = 0.026     normal             
  BCFN(2+15,13-5,T)                 R=  +5.7  p = 0.018     normal             
  BCFN(2+16,13-6,T)                 R=  -3.5  p = 0.945     normal             
  BCFN(2+17,13-6,T)                 R=  -1.7  p = 0.742     normal             
  BCFN(2+18,13-7,T)                 R=  +0.4  p = 0.389     normal             
  BCFN(2+19,13-8,T)                 R=  -2.6  p = 0.903     normal             
  BCFN(2+20,13-8,T)                 R=  -1.1  p = 0.649     normal             
  BCFN(2+21,13-9,T)                 R=  +6.3  p = 0.017     normal             
  BCFN(2+22,13-9,T)                 R=  +1.0  p = 0.264     normal             
  DC6-9x1Bytes-1                    R=  +1.5  p = 0.251     normal             
  Gap-16:A                          R=  +1.7  p = 0.149     normal             
  Gap-16:B                          R=  +2.1  p = 0.065     normal             
  FPF-14+6/16:(0,14-0)              R=  -0.4  p = 0.617     normal             
  FPF-14+6/16:(1,14-0)              R=  -1.0  p = 0.761     normal             
  FPF-14+6/16:(2,14-0)              R=  +1.9  p = 0.089     normal             
  FPF-14+6/16:(3,14-0)              R=  +0.6  p = 0.336     normal             
  FPF-14+6/16:(4,14-0)              R=  -0.8  p = 0.712     normal             
  FPF-14+6/16:(5,14-0)              R=  -0.5  p = 0.639     normal             
  FPF-14+6/16:(6,14-0)              R=  +0.1  p = 0.486     normal             
  FPF-14+6/16:(7,14-0)              R=  +0.8  p = 0.299     normal             
  FPF-14+6/16:(8,14-0)              R=  -0.9  p = 0.734     normal             
  FPF-14+6/16:(9,14-0)              R=  +1.4  p = 0.166     normal             
  FPF-14+6/16:(10,14-0)             R=  -0.3  p = 0.574     normal             
  FPF-14+6/16:(11,14-0)             R=  +2.8  p = 0.024     normal             
  FPF-14+6/16:(12,14-1)             R=  +0.3  p = 0.412     normal             
  FPF-14+6/16:(13,14-2)             R=  +1.0  p = 0.250     normal             
  FPF-14+6/16:(14,14-2)             R=  +1.4  p = 0.168     normal             
  FPF-14+6/16:(15,14-3)             R=  +1.0  p = 0.236     normal             
  FPF-14+6/16:(16,14-4)             R=  +1.2  p = 0.195     normal             
  FPF-14+6/16:(17,14-5)             R=  +1.7  p = 0.112     normal             
  FPF-14+6/16:(18,14-5)             R=  +1.9  p = 0.090     normal             
  FPF-14+6/16:(19,14-6)             R=  -0.5  p = 0.645     normal             
  FPF-14+6/16:(20,14-7)             R=  +1.1  p = 0.223     normal             
  FPF-14+6/16:(21,14-8)             R=  +0.2  p = 0.411     normal             
  FPF-14+6/16:(22,14-8)             R=  +0.3  p = 0.396     normal             
  FPF-14+6/16:(23,14-9)             R=  -1.4  p = 0.851     normal             
  FPF-14+6/16:(24,14-10)            R=  -1.0  p = 0.744     normal             
  FPF-14+6/16:(25,14-11)            R=  +0.4  p = 0.322     normal             
  FPF-14+6/16:(26,14-11)            R=  -2.0  p = 0.971     normal             
  FPF-14+6/16:all                   R=  +1.8  p = 0.114     normal             
  FPF-14+6/16:cross                 R=  +0.2  p = 0.386     normal             
  BRank(12):128(16)                 R=  -1.2  p~= 0.890     normal             
  BRank(12):256(8)                  R=  -0.7  p~= 0.750     normal             
  BRank(12):384(2)                  R=  +0.6  p~= 0.322     normal             
  BRank(12):512(8)                  R=  -0.7  p~= 0.670     normal             
  BRank(12):768(2)                  R=  +1.6  p~= 0.168     normal             
  BRank(12):1K(4)                   R=  -1.4  p~= 0.890     normal             
  BRank(12):1536(1)                 R=  +0.4  p~= 0.366     normal             
  BRank(12):2K(4)                   R=  +1.0  p~= 0.180     normal             
  BRank(12):3K(1)                   R=  +0.4  p~= 0.366     normal             
  BRank(12):4K(2)                   R=  +0.6  p~= 0.322     normal             
  BRank(12):6K(1)                   R=  -0.7  p~= 0.689     normal             
  BRank(12):8K(2)                   R=  -1.0  p~= 0.744     normal             
  BRank(12):12K(1)                  R=  +1.8  p~= 0.146     normal             
  mod3n(5):(0,9-0)                  R=  +1.8  p = 0.182     normal             
  mod3n(5):(1,9-0)                  R=  -0.6  p = 0.614     normal             
  mod3n(5):(2,9-0)                  R=  -2.7  p = 0.909     normal             
  mod3n(5):(3,9-0)                  R=  -0.2  p = 0.549     normal             
  mod3n(5):(4,9-0)                  R=  -1.6  p = 0.785     normal             
  mod3n(5):(5,9-0)                  R=  -2.5  p = 0.897     normal             
  mod3n(5):(6,9-0)                  R=  +1.3  p = 0.253     normal             
  mod3n(5):(7,9-0)                  R=  -3.6  p = 0.965     normal             
  mod3n(5):(8,9-1)                  R=  +3.9  p = 0.028     normal             
  mod3n(5):(9,9-1)                  R=  +0.0  p = 0.491     normal             
  mod3n(5):(10,9-2)                 R=  -0.4  p = 0.581     normal             
  mod3n(5):(11,9-2)                 R=  -3.1  p = 0.941     normal             
  mod3n(5):(12,9-3)                 R=  +2.1  p = 0.153     normal             
  mod3n(5):(13,9-3)                 R=  +1.1  p = 0.280     normal             
  mod3n(5):(14,9-4)                 R=  -0.9  p = 0.663     normal             
  mod3n(5):(15,9-4)                 R=  -2.8  p = 0.933     normal             
  TMFn(2+0):wl                      R=  -1.3  p~= 0.7       normal             
  TMFn(2+1):wl                      R=  -0.7  p~= 0.6       normal             
  TMFn(2+2):wl                      R=  -2.4  p~= 0.8       normal             
  TMFn(2+3):wl                      R=  +0.2  p~= 0.5       normal             
  TMFn(2+4):wl                      R=  -0.8  p~= 0.6       normal             
  TMFn(2+5):wl                      R=  -2.2  p~= 0.8       normal             
  TMFn(2+6):wl                      R=  -0.5  p~= 0.6       normal             
  TMFn(2+7):wl                      R=  +0.9  p~= 0.4       normal             
  TMFn(2+8):wl                      R=  +2.1  p~= 0.2       normal             
  TMFn(2+9):wl                      R=  +1.5  p~= 0.3       normal             
  TMFn(2+10):wl                     R=  -0.7  p~= 0.6       normal             
  TMFn(2+11):wl                     R=  -3.6  p~= 0.9       normal             
  [Low16/64]BCFN(2+0,13-0,T)        R=  -0.2  p = 0.528     normal             
  [Low16/64]BCFN(2+1,13-0,T)        R=  +2.0  p = 0.202     normal             
  [Low16/64]BCFN(2+2,13-0,T)        R=  -2.4  p = 0.833     normal             
  [Low16/64]BCFN(2+3,13-0,T)        R=  +0.2  p = 0.464     normal             
  [Low16/64]BCFN(2+4,13-0,T)        R=  +2.0  p = 0.212     normal             
  [Low16/64]BCFN(2+5,13-0,T)        R=  -1.9  p = 0.778     normal             
  [Low16/64]BCFN(2+6,13-1,T)        R=  -1.6  p = 0.745     normal             
  [Low16/64]BCFN(2+7,13-1,T)        R=  +4.6  p = 0.035     normal             
  [Low16/64]BCFN(2+8,13-2,T)        R=  +2.7  p = 0.136     normal             
  [Low16/64]BCFN(2+9,13-3,T)        R=  +0.9  p = 0.346     normal             
  [Low16/64]BCFN(2+10,13-3,T)       R=  -1.8  p = 0.770     normal             
  [Low16/64]BCFN(2+11,13-4,T)       R=  +1.1  p = 0.305     normal             
  [Low16/64]BCFN(2+12,13-5,T)       R=  +4.5  p = 0.041     normal             
  [Low16/64]BCFN(2+13,13-5,T)       R=  +1.4  p = 0.264     normal             
  [Low16/64]BCFN(2+14,13-6,T)       R=  +1.4  p = 0.253     normal             
  [Low16/64]BCFN(2+15,13-6,T)       R=  +0.4  p = 0.403     normal             
  [Low16/64]BCFN(2+16,13-7,T)       R=  +1.7  p = 0.212     normal             
  [Low16/64]BCFN(2+17,13-8,T)       R=  +2.5  p = 0.138     normal             
  [Low16/64]BCFN(2+18,13-8,T)       R=  +3.8  p = 0.065     normal             
  [Low16/64]BCFN(2+19,13-9,T)       R=  -0.9  p = 0.601     normal             
  [Low16/64]BCFN(2+20,13-9,T)       R=  -0.1  p = 0.429     normal             
  [Low16/64]DC6-9x1Bytes-1          R=  -0.8  p = 0.709     normal             
  [Low16/64]Gap-16:A                R=  -1.2  p = 0.853     normal             
  [Low16/64]Gap-16:B                R=  -2.9  p = 0.980     normal             
  [Low16/64]FPF-14+6/16:(0,14-0)    R=  -2.1  p = 0.930     normal             
  [Low16/64]FPF-14+6/16:(1,14-0)    R=  -2.0  p = 0.925     normal             
  [Low16/64]FPF-14+6/16:(2,14-0)    R=  +0.2  p = 0.447     normal             
  [Low16/64]FPF-14+6/16:(3,14-0)    R=  +0.5  p = 0.367     normal             
  [Low16/64]FPF-14+6/16:(4,14-0)    R=  -0.0  p = 0.508     normal             
  [Low16/64]FPF-14+6/16:(5,14-0)    R=  +2.8  p = 0.027     normal             
  [Low16/64]FPF-14+6/16:(6,14-0)    R=  +1.7  p = 0.111     normal             
  [Low16/64]FPF-14+6/16:(7,14-0)    R=  -0.8  p = 0.725     normal             
  [Low16/64]FPF-14+6/16:(8,14-0)    R=  -0.6  p = 0.655     normal             
  [Low16/64]FPF-14+6/16:(9,14-0)    R=  -1.7  p = 0.889     normal             
  [Low16/64]FPF-14+6/16:(10,14-1)   R=  -0.1  p = 0.520     normal             
  [Low16/64]FPF-14+6/16:(11,14-2)   R=  +1.2  p = 0.201     normal             
  [Low16/64]FPF-14+6/16:(12,14-2)   R=  +0.6  p = 0.341     normal             
  [Low16/64]FPF-14+6/16:(13,14-3)   R=  +1.4  p = 0.162     normal             
  [Low16/64]FPF-14+6/16:(14,14-4)   R=  +3.3  p = 0.013     normal             
  [Low16/64]FPF-14+6/16:(15,14-5)   R=  +0.6  p = 0.335     normal             
  [Low16/64]FPF-14+6/16:(16,14-5)   R=  -0.3  p = 0.578     normal             
  [Low16/64]FPF-14+6/16:(17,14-6)   R=  +3.6  p =  8.7e-3   normal             
  [Low16/64]FPF-14+6/16:(18,14-7)   R=  +0.7  p = 0.313     normal             
  [Low16/64]FPF-14+6/16:(19,14-8)   R=  +0.5  p = 0.349     normal             
  [Low16/64]FPF-14+6/16:(20,14-8)   R=  +1.7  p = 0.126     normal             
  [Low16/64]FPF-14+6/16:(21,14-9)   R=  -1.6  p = 0.890     normal             
  [Low16/64]FPF-14+6/16:(22,14-10)  R=  +0.4  p = 0.357     normal             
  [Low16/64]FPF-14+6/16:(23,14-11)  R=  -0.2  p = 0.476     normal             
  [Low16/64]FPF-14+6/16:(24,14-11)  R=  -0.0  p = 0.443     normal             
  [Low16/64]FPF-14+6/16:all         R=  +0.3  p = 0.434     normal             
  [Low16/64]FPF-14+6/16:cross       R=  +1.1  p = 0.138     normal             
  [Low16/64]BRank(12):128(8)        R=  +0.1  p~= 0.470     normal             
  [Low16/64]BRank(12):256(8)        R=  -1.9  p~= 0.990     normal             
  [Low16/64]BRank(12):384(2)        R=  -1.0  p~= 0.744     normal             
  [Low16/64]BRank(12):512(4)        R=  -0.8  p~= 0.670     normal             
  [Low16/64]BRank(12):768(2)        R=  +4.5  p~= 0.023     normal             
  [Low16/64]BRank(12):1K(4)         R=  -0.1  p~= 0.490     normal             
  [Low16/64]BRank(12):1536(1)       R=  +0.4  p~= 0.366     normal             
  [Low16/64]BRank(12):2K(2)         R=  -1.0  p~= 0.744     normal             
  [Low16/64]BRank(12):3K(1)         R=  -0.7  p~= 0.689     normal             
  [Low16/64]BRank(12):4K(2)         R=  -0.2  p~= 0.554     normal             
  [Low16/64]BRank(12):6K(1)         R=  -0.7  p~= 0.689     normal             
  [Low16/64]BRank(12):8K(1)         R=  -0.7  p~= 0.689     normal             
  [Low16/64]mod3n(5):(0,9-0)        R=  +2.6  p = 0.105     normal             
  [Low16/64]mod3n(5):(1,9-0)        R=  +0.8  p = 0.350     normal             
  [Low16/64]mod3n(5):(2,9-0)        R=  +2.0  p = 0.162     normal             
  [Low16/64]mod3n(5):(3,9-0)        R=  -0.8  p = 0.665     normal             
  [Low16/64]mod3n(5):(4,9-0)        R=  -2.4  p = 0.881     normal             
  [Low16/64]mod3n(5):(5,9-0)        R=  +2.1  p = 0.154     normal             
  [Low16/64]mod3n(5):(6,9-1)        R=  +1.5  p = 0.226     normal             
  [Low16/64]mod3n(5):(7,9-1)        R=  -0.6  p = 0.614     normal             
  [Low16/64]mod3n(5):(8,9-2)        R=  +2.0  p = 0.164     normal             
  [Low16/64]mod3n(5):(9,9-2)        R=  -0.5  p = 0.591     normal             
  [Low16/64]mod3n(5):(10,9-3)       R=  -1.4  p = 0.758     normal             
  [Low16/64]mod3n(5):(11,9-3)       R=  -1.3  p = 0.735     normal             
  [Low16/64]mod3n(5):(12,9-4)       R=  +0.1  p = 0.453     normal             
  [Low16/64]mod3n(5):(13,9-4)       R=  -0.9  p = 0.658     normal             
  [Low16/64]mod3n(5):(14,9-5)       R=  -2.3  p = 0.894     normal             
  [Low16/64]mod3n(5):(15,9-5)       R=  -0.9  p = 0.655     normal             
  [Low16/64]TMFn(2+0):wl            R=  -1.5  p~= 0.7       normal             
  [Low16/64]TMFn(2+1):wl            R=  -2.0  p~= 0.7       normal             
  [Low16/64]TMFn(2+2):wl            R=  -1.1  p~= 0.7       normal             
  [Low16/64]TMFn(2+3):wl            R=  -0.5  p~= 0.6       normal             
  [Low16/64]TMFn(2+4):wl            R=  +0.1  p~= 0.5       normal             
  [Low16/64]TMFn(2+5):wl            R=  -0.8  p~= 0.6       normal             
  [Low16/64]TMFn(2+6):wl            R=  -0.2  p~= 0.5       normal             
  [Low16/64]TMFn(2+7):wl            R=  -0.3  p~= 0.5       normal             
  [Low16/64]TMFn(2+8):wl            R=  -1.3  p~= 0.7       normal             
  [Low16/64]TMFn(2+9):wl            R=  -0.2  p~= 0.5       normal             
  [Low4/64]BCFN(2+0,13-0,T)         R=  +1.9  p = 0.214     normal             
  [Low4/64]BCFN(2+1,13-0,T)         R=  -0.9  p = 0.642     normal             
  [Low4/64]BCFN(2+2,13-0,T)         R=  -1.0  p = 0.653     normal             
  [Low4/64]BCFN(2+3,13-0,T)         R=  +1.1  p = 0.318     normal             
  [Low4/64]BCFN(2+4,13-1,T)         R=  +1.1  p = 0.324     normal             
  [Low4/64]BCFN(2+5,13-1,T)         R=  -2.0  p = 0.795     normal             
  [Low4/64]BCFN(2+6,13-2,T)         R=  +0.9  p = 0.352     normal             
  [Low4/64]BCFN(2+7,13-3,T)         R=  +4.9  p = 0.028     normal             
  [Low4/64]BCFN(2+8,13-3,T)         R=  +0.2  p = 0.448     normal             
  [Low4/64]BCFN(2+9,13-4,T)         R=  -0.8  p = 0.609     normal             
  [Low4/64]BCFN(2+10,13-5,T)        R=  +1.5  p = 0.247     normal             
  [Low4/64]BCFN(2+11,13-5,T)        R=  +0.8  p = 0.354     normal             
  [Low4/64]BCFN(2+12,13-6,T)        R=  +0.9  p = 0.324     normal             
  [Low4/64]BCFN(2+13,13-6,T)        R=  +2.9  p = 0.117     normal             
  [Low4/64]BCFN(2+14,13-7,T)        R=  -3.0  p = 0.924     normal             
  [Low4/64]BCFN(2+15,13-8,T)        R=  +5.2  p = 0.031     normal             
  [Low4/64]BCFN(2+16,13-8,T)        R=  -1.7  p = 0.759     normal             
  [Low4/64]BCFN(2+17,13-9,T)        R=  -1.0  p = 0.627     normal             
  [Low4/64]BCFN(2+18,13-9,T)        R=  +0.2  p = 0.382     normal             
  [Low4/64]DC6-9x1Bytes-1           R=  +0.1  p = 0.560     normal             
  [Low4/64]Gap-16:A                 R=  -1.0  p = 0.833     normal             
  [Low4/64]Gap-16:B                 R=  -1.9  p = 0.911     normal             
  [Low4/64]FPF-14+6/16:(0,14-0)     R=  -0.3  p = 0.573     normal             
  [Low4/64]FPF-14+6/16:(1,14-0)     R=  +1.7  p = 0.112     normal             
  [Low4/64]FPF-14+6/16:(2,14-0)     R=  +0.7  p = 0.302     normal             
  [Low4/64]FPF-14+6/16:(3,14-0)     R=  +0.2  p = 0.441     normal             
  [Low4/64]FPF-14+6/16:(4,14-0)     R=  +1.7  p = 0.113     normal             
  [Low4/64]FPF-14+6/16:(5,14-0)     R=  -1.2  p = 0.807     normal             
  [Low4/64]FPF-14+6/16:(6,14-0)     R=  -0.5  p = 0.644     normal             
  [Low4/64]FPF-14+6/16:(7,14-0)     R=  +0.9  p = 0.258     normal             
  [Low4/64]FPF-14+6/16:(8,14-1)     R=  -0.6  p = 0.671     normal             
  [Low4/64]FPF-14+6/16:(9,14-2)     R=  +2.3  p = 0.058     normal             
  [Low4/64]FPF-14+6/16:(10,14-2)    R=  -1.8  p = 0.898     normal             
  [Low4/64]FPF-14+6/16:(11,14-3)    R=  +2.5  p = 0.039     normal             
  [Low4/64]FPF-14+6/16:(12,14-4)    R=  +1.4  p = 0.157     normal             
  [Low4/64]FPF-14+6/16:(13,14-5)    R=  +1.9  p = 0.096     normal             
  [Low4/64]FPF-14+6/16:(14,14-5)    R=  +0.0  p = 0.492     normal             
  [Low4/64]FPF-14+6/16:(15,14-6)    R=  +1.8  p = 0.103     normal             
  [Low4/64]FPF-14+6/16:(16,14-7)    R=  +2.2  p = 0.070     normal             
  [Low4/64]FPF-14+6/16:(17,14-8)    R=  +0.2  p = 0.412     normal             
  [Low4/64]FPF-14+6/16:(18,14-8)    R=  +0.3  p = 0.405     normal             
  [Low4/64]FPF-14+6/16:(19,14-9)    R=  +1.3  p = 0.179     normal             
  [Low4/64]FPF-14+6/16:(20,14-10)   R=  -0.3  p = 0.532     normal             
  [Low4/64]FPF-14+6/16:(21,14-11)   R=  +1.0  p = 0.210     normal             
  [Low4/64]FPF-14+6/16:(22,14-11)   R=  +6.6  p =  1.0e-3   normal             
  [Low4/64]FPF-14+6/16:all          R=  +1.8  p = 0.110     normal             
  [Low4/64]FPF-14+6/16:cross        R=  +1.0  p = 0.160     normal             
  [Low4/64]BRank(12):128(8)         R=  -0.7  p~= 0.670     normal             
  [Low4/64]BRank(12):256(4)         R=  -1.4  p~= 0.890     normal             
  [Low4/64]BRank(12):384(2)         R=  -1.0  p~= 0.744     normal             
  [Low4/64]BRank(12):512(4)         R=  -0.2  p~= 0.500     normal             
  [Low4/64]BRank(12):768(1)         R=  -0.7  p~= 0.689     normal             
  [Low4/64]BRank(12):1K(2)          R=  -1.0  p~= 0.744     normal             
  [Low4/64]BRank(12):1536(1)        R=  -0.7  p~= 0.689     normal             
  [Low4/64]BRank(12):2K(2)          R=  -0.2  p~= 0.554     normal             
  [Low4/64]BRank(12):3K(1)          R=  +0.4  p~= 0.366     normal             
  [Low4/64]BRank(12):4K(1)          R=  +0.4  p~= 0.366     normal             
  [Low4/64]mod3n(5):(0,9-0)         R=  -1.1  p = 0.705     normal             
  [Low4/64]mod3n(5):(1,9-0)         R=  -0.2  p = 0.538     normal             
  [Low4/64]mod3n(5):(2,9-0)         R=  -0.3  p = 0.570     normal             
  [Low4/64]mod3n(5):(3,9-0)         R=  +2.4  p = 0.121     normal             
  [Low4/64]mod3n(5):(4,9-1)         R=  -0.5  p = 0.598     normal             
  [Low4/64]mod3n(5):(5,9-1)         R=  -0.9  p = 0.679     normal             
  [Low4/64]mod3n(5):(6,9-2)         R=  +2.7  p = 0.092     normal             
  [Low4/64]mod3n(5):(7,9-2)         R=  +0.3  p = 0.438     normal             
  [Low4/64]mod3n(5):(8,9-3)         R=  +1.3  p = 0.252     normal             
  [Low4/64]mod3n(5):(9,9-3)         R=  +0.7  p = 0.361     normal             
  [Low4/64]mod3n(5):(10,9-4)        R=  +1.5  p = 0.212     normal             
  [Low4/64]mod3n(5):(11,9-4)        R=  -2.7  p = 0.923     normal             
  [Low4/64]mod3n(5):(12,9-5)        R=  +0.6  p = 0.353     normal             
  [Low4/64]mod3n(5):(13,9-5)        R=  -1.6  p = 0.800     normal             
  [Low4/64]mod3n(5):(14,9-6)        R=  +4.9  p = 0.017     normal             
  [Low4/64]mod3n(5):(15,9-6)        R=  +0.3  p = 0.389     normal             
  [Low4/64]TMFn(2+0):wl             R=  -1.1  p~= 0.7       normal             
  [Low4/64]TMFn(2+1):wl             R=  -1.3  p~= 0.7       normal             
  [Low4/64]TMFn(2+2):wl             R=  -0.3  p~= 0.5       normal             
  [Low4/64]TMFn(2+3):wl             R=  -1.1  p~= 0.7       normal             
  [Low4/64]TMFn(2+4):wl             R=  +0.8  p~= 0.4       normal             
  [Low4/64]TMFn(2+5):wl             R=  +0.5  p~= 0.4       normal             
  [Low4/64]TMFn(2+6):wl             R=  -0.9  p~= 0.6       normal             
  [Low4/64]TMFn(2+7):wl             R=  +0.1  p~= 0.5       normal             
  [Low1/64]BCFN(2+0,13-1,T)         R=  +3.8  p = 0.066     normal             
  [Low1/64]BCFN(2+1,13-1,T)         R=  +2.9  p = 0.118     normal             
  [Low1/64]BCFN(2+2,13-1,T)         R=  -2.9  p = 0.882     normal             
  [Low1/64]BCFN(2+3,13-1,T)         R=  -0.8  p = 0.627     normal             
  [Low1/64]BCFN(2+4,13-2,T)         R=  +2.6  p = 0.143     normal             
  [Low1/64]BCFN(2+5,13-3,T)         R=  +0.5  p = 0.409     normal             
  [Low1/64]BCFN(2+6,13-3,T)         R=  +0.9  p = 0.349     normal             
  [Low1/64]BCFN(2+7,13-4,T)         R=  +1.0  p = 0.326     normal             
  [Low1/64]BCFN(2+8,13-5,T)         R=  +4.5  p = 0.043     normal             
  [Low1/64]BCFN(2+9,13-5,T)         R=  +1.0  p = 0.321     normal             
  [Low1/64]BCFN(2+10,13-6,T)        R=  -2.1  p = 0.802     normal             
  [Low1/64]BCFN(2+11,13-6,T)        R=  -1.1  p = 0.648     normal             
  [Low1/64]BCFN(2+12,13-7,T)        R=  +3.5  p = 0.081     normal             
  [Low1/64]BCFN(2+13,13-8,T)        R=  +0.6  p = 0.332     normal             
  [Low1/64]BCFN(2+14,13-8,T)        R=  +2.6  p = 0.129     normal             
  [Low1/64]BCFN(2+15,13-9,T)        R=  +3.2  p = 0.087     normal             
  [Low1/64]BCFN(2+16,13-9,T)        R=  -0.3  p = 0.477     normal             
  [Low1/64]DC6-9x1Bytes-1           R=  -1.2  p = 0.817     normal             
  [Low1/64]Gap-16:A                 R=  -0.5  p = 0.741     normal             
  [Low1/64]Gap-16:B                 R=  +0.7  p = 0.296     normal             
  [Low1/64]FPF-14+6/16:(0,14-0)     R=  +1.9  p = 0.096     normal             
  [Low1/64]FPF-14+6/16:(1,14-0)     R=  -0.6  p = 0.657     normal             
  [Low1/64]FPF-14+6/16:(2,14-0)     R=  -1.1  p = 0.785     normal             
  [Low1/64]FPF-14+6/16:(3,14-0)     R=  +1.3  p = 0.178     normal             
  [Low1/64]FPF-14+6/16:(4,14-0)     R=  +1.1  p = 0.229     normal             
  [Low1/64]FPF-14+6/16:(5,14-0)     R=  -1.6  p = 0.866     normal             
  [Low1/64]FPF-14+6/16:(6,14-1)     R=  -0.8  p = 0.708     normal             
  [Low1/64]FPF-14+6/16:(7,14-2)     R=  +0.9  p = 0.266     normal             
  [Low1/64]FPF-14+6/16:(8,14-2)     R=  -0.1  p = 0.522     normal             
  [Low1/64]FPF-14+6/16:(9,14-3)     R=  -1.3  p = 0.827     normal             
  [Low1/64]FPF-14+6/16:(10,14-4)    R=  -0.6  p = 0.675     normal             
  [Low1/64]FPF-14+6/16:(11,14-5)    R=  +1.2  p = 0.198     normal             
  [Low1/64]FPF-14+6/16:(12,14-5)    R=  -1.5  p = 0.862     normal             
  [Low1/64]FPF-14+6/16:(13,14-6)    R=  -0.4  p = 0.598     normal             
  [Low1/64]FPF-14+6/16:(14,14-7)    R=  -2.0  p = 0.931     normal             
  [Low1/64]FPF-14+6/16:(15,14-8)    R=  +0.4  p = 0.380     normal             
  [Low1/64]FPF-14+6/16:(16,14-8)    R=  -0.5  p = 0.616     normal             
  [Low1/64]FPF-14+6/16:(17,14-9)    R=  -1.8  p = 0.912     normal             
  [Low1/64]FPF-14+6/16:(18,14-10)   R=  +0.6  p = 0.313     normal             
  [Low1/64]FPF-14+6/16:(19,14-11)   R=  -2.3  p =1-5.1e-3   normal             
  [Low1/64]FPF-14+6/16:(20,14-11)   R=  +1.2  p = 0.176     normal             
  [Low1/64]FPF-14+6/16:all          R=  -0.0  p = 0.530     normal             
  [Low1/64]FPF-14+6/16:cross        R=  +1.3  p = 0.099     normal             
  [Low1/64]BRank(12):128(8)         R=  +2.5  p~= 0.020     normal             
  [Low1/64]BRank(12):256(4)         R=  +0.4  p~= 0.340     normal             
  [Low1/64]BRank(12):384(1)         R=  +0.4  p~= 0.366     normal             
  [Low1/64]BRank(12):512(4)         R=  -0.2  p~= 0.500     normal             
  [Low1/64]BRank(12):768(1)         R=  -0.7  p~= 0.689     normal             
  [Low1/64]BRank(12):1K(2)          R=  +6.2  p~=  6.7e-3   normal             
  [Low1/64]BRank(12):1536(1)        R=  -0.7  p~= 0.689     normal             
  [Low1/64]BRank(12):2K(2)          R=  -0.2  p~= 0.554     normal             
  [Low1/64]BRank(12):3K(1)          R=  +0.4  p~= 0.366     normal             
  [Low1/64]mod3n(5):(0,9-0)         R=  +1.0  p = 0.315     normal             
  [Low1/64]mod3n(5):(1,9-0)         R=  -4.4  p = 0.986     normal             
  [Low1/64]mod3n(5):(2,9-1)         R=  -2.0  p = 0.842     normal             
  [Low1/64]mod3n(5):(3,9-1)         R=  +0.3  p = 0.434     normal             
  [Low1/64]mod3n(5):(4,9-2)         R=  +1.4  p = 0.232     normal             
  [Low1/64]mod3n(5):(5,9-2)         R=  +2.7  p = 0.091     normal             
  [Low1/64]mod3n(5):(6,9-3)         R=  +6.7  p =  1.2e-3   normal             
  [Low1/64]mod3n(5):(7,9-3)         R=  +1.2  p = 0.272     normal             
  [Low1/64]mod3n(5):(8,9-4)         R=  -0.8  p = 0.646     normal             
  [Low1/64]mod3n(5):(9,9-4)         R=  -2.7  p = 0.923     normal             
  [Low1/64]mod3n(5):(10,9-5)        R=  -2.0  p = 0.858     normal             
  [Low1/64]mod3n(5):(11,9-5)        R=  -3.4  p = 0.980     normal             
  [Low1/64]mod3n(5):(12,9-6)        R=  +0.8  p = 0.292     normal             
  [Low1/64]mod3n(5):(13,9-6)        R=  +2.2  p = 0.122     normal             
  [Low1/64]mod3n(5):(14,9-6)        R=  +0.9  p = 0.277     normal             
  [Low1/64]mod3n(5):(15,9-6)        R=  -0.8  p = 0.616     normal             
  [Low1/64]TMFn(2+0):wl             R=  -0.7  p~= 0.6       normal             
  [Low1/64]TMFn(2+1):wl             R=  +0.0  p~= 0.5       normal             
  [Low1/64]TMFn(2+2):wl             R=  -1.6  p~= 0.7       normal             
  [Low1/64]TMFn(2+3):wl             R=  +3.5  p~= 0.1       normal             
  [Low1/64]TMFn(2+4):wl             R=  -1.9  p~= 0.7       normal             
  [Low1/64]TMFn(2+5):wl             R=  +1.0  p~= 0.4       normal             
  
rng=RNG_stdin64, seed=unknown  
length= 128 gigabytes (2^37 bytes), time= 1202 seconds  
  Test Name                         Raw       Processed     Evaluation  
  BCFN(2+0,13-0,T)                  R=  +1.7  p = 0.243     normal             
  BCFN(2+1,13-0,T)                  R=  +2.5  p = 0.157     normal             
  BCFN(2+2,13-0,T)                  R=  +0.5  p = 0.408     normal             
  BCFN(2+3,13-0,T)                  R=  +3.3  p = 0.093     normal             
  BCFN(2+4,13-0,T)                  R=  +3.6  p = 0.074     normal             
  BCFN(2+5,13-0,T)                  R=  +4.0  p = 0.052     normal             
  BCFN(2+6,13-0,T)                  R=  -2.3  p = 0.826     normal             
  BCFN(2+7,13-0,T)                  R=  -1.1  p = 0.664     normal             
  BCFN(2+8,13-0,T)                  R=  +2.0  p = 0.205     normal             
  BCFN(2+9,13-1,T)                  R=  -1.1  p = 0.664     normal             
  BCFN(2+10,13-1,T)                 R=  -0.8  p = 0.619     normal             
  BCFN(2+11,13-2,T)                 R=  +3.9  p = 0.058     normal             
  BCFN(2+12,13-3,T)                 R=  +2.5  p = 0.153     normal             
  BCFN(2+13,13-3,T)                 R=  +0.2  p = 0.456     normal             
  BCFN(2+14,13-4,T)                 R=  +2.6  p = 0.148     normal             
  BCFN(2+15,13-5,T)                 R=  +1.9  p = 0.207     normal             
  BCFN(2+16,13-5,T)                 R=  -3.6  p = 0.945     normal             
  BCFN(2+17,13-6,T)                 R=  -2.4  p = 0.842     normal             
  BCFN(2+18,13-6,T)                 R=  +5.5  p = 0.022     normal             
  BCFN(2+19,13-7,T)                 R=  -1.5  p = 0.721     normal             
  BCFN(2+20,13-8,T)                 R=  +2.1  p = 0.162     normal             
  BCFN(2+21,13-8,T)                 R=  +6.2  p = 0.017     normal             
  BCFN(2+22,13-9,T)                 R=  -0.6  p = 0.538     normal             
  BCFN(2+23,13-9,T)                 R=  +2.3  p = 0.139     normal             
  DC6-9x1Bytes-1                    R=  +2.0  p = 0.168     normal             
  Gap-16:A                          R=  +2.2  p = 0.089     normal             
  Gap-16:B                          R=  +1.9  p = 0.088     normal             
  FPF-14+6/16:(0,14-0)              R=  -0.5  p = 0.652     normal             
  FPF-14+6/16:(1,14-0)              R=  -0.5  p = 0.628     normal             
  FPF-14+6/16:(2,14-0)              R=  +1.6  p = 0.129     normal             
  FPF-14+6/16:(3,14-0)              R=  -0.5  p = 0.626     normal             
  FPF-14+6/16:(4,14-0)              R=  -1.6  p = 0.875     normal             
  FPF-14+6/16:(5,14-0)              R=  -0.3  p = 0.591     normal             
  FPF-14+6/16:(6,14-0)              R=  -0.6  p = 0.673     normal             
  FPF-14+6/16:(7,14-0)              R=  +2.8  p = 0.025     normal             
  FPF-14+6/16:(8,14-0)              R=  -1.4  p = 0.838     normal             
  FPF-14+6/16:(9,14-0)              R=  -0.7  p = 0.688     normal             
  FPF-14+6/16:(10,14-0)             R=  +3.0  p = 0.017     normal             
  FPF-14+6/16:(11,14-0)             R=  +2.5  p = 0.038     normal             
  FPF-14+6/16:(12,14-0)             R=  +1.2  p = 0.199     normal             
  FPF-14+6/16:(13,14-1)             R=  +2.2  p = 0.064     normal             
  FPF-14+6/16:(14,14-2)             R=  -0.4  p = 0.603     normal             
  FPF-14+6/16:(15,14-2)             R=  -0.2  p = 0.569     normal             
  FPF-14+6/16:(16,14-3)             R=  +1.4  p = 0.171     normal             
  FPF-14+6/16:(17,14-4)             R=  +2.8  p = 0.026     normal             
  FPF-14+6/16:(18,14-5)             R=  +0.4  p = 0.394     normal             
  FPF-14+6/16:(19,14-5)             R=  -3.3  p =1-6.8e-3   normal             
  FPF-14+6/16:(20,14-6)             R=  -0.5  p = 0.646     normal             
  FPF-14+6/16:(21,14-7)             R=  +0.0  p = 0.486     normal             
  FPF-14+6/16:(22,14-8)             R=  -1.3  p = 0.812     normal             
  FPF-14+6/16:(23,14-8)             R=  -2.2  p = 0.949     normal             
  FPF-14+6/16:(24,14-9)             R=  -1.8  p = 0.907     normal             
  FPF-14+6/16:(25,14-10)            R=  +1.1  p = 0.197     normal             
  FPF-14+6/16:(26,14-11)            R=  -0.8  p = 0.678     normal             
  FPF-14+6/16:(27,14-11)            R=  -0.3  p = 0.521     normal             
  FPF-14+6/16:all                   R=  +1.8  p = 0.109     normal             
  FPF-14+6/16:cross                 R=  +1.1  p = 0.130     normal             
  BRank(12):128(16)                 R=  -1.2  p~= 0.890     normal             
  BRank(12):256(8)                  R=  -0.7  p~= 0.750     normal             
  BRank(12):384(4)                  R=  +0.3  p~= 0.400     normal             
  BRank(12):512(8)                  R=  -0.7  p~= 0.670     normal             
  BRank(12):768(2)                  R=  +1.6  p~= 0.168     normal             
  BRank(12):1K(4)                   R=  -1.4  p~= 0.890     normal             
  BRank(12):1536(2)                 R=  -0.2  p~= 0.554     normal             
  BRank(12):2K(4)                   R=  +1.0  p~= 0.180     normal             
  BRank(12):3K(1)                   R=  +0.4  p~= 0.366     normal             
  BRank(12):4K(2)                   R=  +0.6  p~= 0.322     normal             
  BRank(12):6K(1)                   R=  -0.7  p~= 0.689     normal             
  BRank(12):8K(2)                   R=  -1.0  p~= 0.744     normal             
  BRank(12):12K(1)                  R=  +1.8  p~= 0.146     normal             
  BRank(12):16K(1)                  R=  -0.7  p~= 0.689     normal             
  mod3n(5):(0,9-0)                  R=  -0.0  p = 0.508     normal             
  mod3n(5):(1,9-0)                  R=  -1.1  p = 0.702     normal             
  mod3n(5):(2,9-0)                  R=  -2.8  p = 0.920     normal             
  mod3n(5):(3,9-0)                  R=  -0.4  p = 0.588     normal             
  mod3n(5):(4,9-0)                  R=  -1.7  p = 0.798     normal             
  mod3n(5):(5,9-0)                  R=  -0.9  p = 0.678     normal             
  mod3n(5):(6,9-0)                  R=  +0.1  p = 0.478     normal             
  mod3n(5):(7,9-0)                  R=  +1.3  p = 0.265     normal             
  mod3n(5):(8,9-0)                  R=  +1.0  p = 0.306     normal             
  mod3n(5):(9,9-1)                  R=  +0.3  p = 0.428     normal             
  mod3n(5):(10,9-1)                 R=  -0.0  p = 0.500     normal             
  mod3n(5):(11,9-2)                 R=  -1.5  p = 0.770     normal             
  mod3n(5):(12,9-2)                 R=  -1.5  p = 0.777     normal             
  mod3n(5):(13,9-3)                 R=  +1.0  p = 0.301     normal             
  mod3n(5):(14,9-3)                 R=  -1.0  p = 0.681     normal             
  mod3n(5):(15,9-4)                 R=  +0.8  p = 0.330     normal             
  TMFn(2+0):wl                      R=  +0.4  p~= 0.4       normal             
  TMFn(2+1):wl                      R=  -0.8  p~= 0.6       normal             
  TMFn(2+2):wl                      R=  -0.4  p~= 0.6       normal             
  TMFn(2+3):wl                      R=  +1.7  p~= 0.3       normal             
  TMFn(2+4):wl                      R=  -3.0  p~= 0.8       normal             
  TMFn(2+5):wl                      R=  -0.8  p~= 0.6       normal             
  TMFn(2+6):wl                      R=  -1.2  p~= 0.7       normal             
  TMFn(2+7):wl                      R=  +3.1  p~= 0.2       normal             
  TMFn(2+8):wl                      R=  +0.3  p~= 0.5       normal             
  TMFn(2+9):wl                      R=  -2.7  p~= 0.8       normal             
  TMFn(2+10):wl                     R=  +0.1  p~= 0.5       normal             
  TMFn(2+11):wl                     R=  -0.8  p~= 0.6       normal             
  TMFn(2+12):wl                     R=  -0.3  p~= 0.5       normal             
  [Low16/64]BCFN(2+0,13-0,T)        R=  +1.9  p = 0.217     normal             
  [Low16/64]BCFN(2+1,13-0,T)        R=  +1.9  p = 0.220     normal             
  [Low16/64]BCFN(2+2,13-0,T)        R=  -2.2  p = 0.812     normal             
  [Low16/64]BCFN(2+3,13-0,T)        R=  +0.5  p = 0.421     normal             
  [Low16/64]BCFN(2+4,13-0,T)        R=  -0.2  p = 0.535     normal             
  [Low16/64]BCFN(2+5,13-0,T)        R=  +6.4  p =  5.7e-3   normal             
  [Low16/64]BCFN(2+6,13-0,T)        R=  -0.4  p = 0.556     normal             
  [Low16/64]BCFN(2+7,13-1,T)        R=  +2.7  p = 0.138     normal             
  [Low16/64]BCFN(2+8,13-1,T)        R=  +5.0  p = 0.024     normal             
  [Low16/64]BCFN(2+9,13-2,T)        R=  +0.1  p = 0.478     normal             
  [Low16/64]BCFN(2+10,13-3,T)       R=  -2.5  p = 0.843     normal             
  [Low16/64]BCFN(2+11,13-3,T)       R=  -1.5  p = 0.726     normal             
  [Low16/64]BCFN(2+12,13-4,T)       R=  -1.9  p = 0.785     normal             
  [Low16/64]BCFN(2+13,13-5,T)       R=  -0.5  p = 0.552     normal             
  [Low16/64]BCFN(2+14,13-5,T)       R=  +0.6  p = 0.377     normal             
  [Low16/64]BCFN(2+15,13-6,T)       R=  +1.0  p = 0.309     normal             
  [Low16/64]BCFN(2+16,13-6,T)       R=  +4.3  p = 0.049     normal             
  [Low16/64]BCFN(2+17,13-7,T)       R=  +3.9  p = 0.066     normal             
  [Low16/64]BCFN(2+18,13-8,T)       R=  +2.0  p = 0.171     normal             
  [Low16/64]BCFN(2+19,13-8,T)       R=  +0.0  p = 0.432     normal             
  [Low16/64]BCFN(2+20,13-9,T)       R=  -0.8  p = 0.565     normal             
  [Low16/64]BCFN(2+21,13-9,T)       R=  +3.7  p = 0.066     normal             
  [Low16/64]DC6-9x1Bytes-1          R=  +0.0  p = 0.526     normal             
  [Low16/64]Gap-16:A                R=  -2.4  p = 0.973     normal             
  [Low16/64]Gap-16:B                R=  -3.1  p = 0.986     normal             
  [Low16/64]FPF-14+6/16:(0,14-0)    R=  +0.6  p = 0.346     normal             
  [Low16/64]FPF-14+6/16:(1,14-0)    R=  -1.0  p = 0.753     normal             
  [Low16/64]FPF-14+6/16:(2,14-0)    R=  +0.5  p = 0.373     normal             
  [Low16/64]FPF-14+6/16:(3,14-0)    R=  +0.1  p = 0.475     normal             
  [Low16/64]FPF-14+6/16:(4,14-0)    R=  -1.2  p = 0.793     normal             
  [Low16/64]FPF-14+6/16:(5,14-0)    R=  +1.9  p = 0.092     normal             
  [Low16/64]FPF-14+6/16:(6,14-0)    R=  +0.5  p = 0.376     normal             
  [Low16/64]FPF-14+6/16:(7,14-0)    R=  -2.7  p = 0.974     normal             
  [Low16/64]FPF-14+6/16:(8,14-0)    R=  +0.6  p = 0.342     normal             
  [Low16/64]FPF-14+6/16:(9,14-0)    R=  -2.7  p = 0.971     normal             
  [Low16/64]FPF-14+6/16:(10,14-0)   R=  -0.2  p = 0.544     normal             
  [Low16/64]FPF-14+6/16:(11,14-1)   R=  +2.1  p = 0.069     normal             
  [Low16/64]FPF-14+6/16:(12,14-2)   R=  -0.1  p = 0.533     normal             
  [Low16/64]FPF-14+6/16:(13,14-2)   R=  +1.5  p = 0.151     normal             
  [Low16/64]FPF-14+6/16:(14,14-3)   R=  +2.5  p = 0.043     normal             
  [Low16/64]FPF-14+6/16:(15,14-4)   R=  +1.9  p = 0.096     normal             
  [Low16/64]FPF-14+6/16:(16,14-5)   R=  +0.9  p = 0.263     normal             
  [Low16/64]FPF-14+6/16:(17,14-5)   R=  +1.9  p = 0.092     normal             
  [Low16/64]FPF-14+6/16:(18,14-6)   R=  +0.6  p = 0.335     normal             
  [Low16/64]FPF-14+6/16:(19,14-7)   R=  -0.8  p = 0.714     normal             
  [Low16/64]FPF-14+6/16:(20,14-8)   R=  -2.7  p = 0.983     normal             
  [Low16/64]FPF-14+6/16:(21,14-8)   R=  -0.9  p = 0.721     normal             
  [Low16/64]FPF-14+6/16:(22,14-9)   R=  +1.6  p = 0.127     normal             
  [Low16/64]FPF-14+6/16:(23,14-10)  R=  +1.5  p = 0.150     normal             
  [Low16/64]FPF-14+6/16:(24,14-11)  R=  -1.7  p = 0.927     normal             
  [Low16/64]FPF-14+6/16:(25,14-11)  R=  +1.0  p = 0.208     normal             
  [Low16/64]FPF-14+6/16:all         R=  +0.1  p = 0.480     normal             
  [Low16/64]FPF-14+6/16:cross       R=  -0.3  p = 0.593     normal             
  [Low16/64]BRank(12):128(8)        R=  +0.1  p~= 0.470     normal             
  [Low16/64]BRank(12):256(8)        R=  -1.9  p~= 0.990     normal             
  [Low16/64]BRank(12):384(2)        R=  -1.0  p~= 0.744     normal             
  [Low16/64]BRank(12):512(4)        R=  -0.8  p~= 0.670     normal             
  [Low16/64]BRank(12):768(2)        R=  +4.5  p~= 0.023     normal             
  [Low16/64]BRank(12):1K(4)         R=  -0.1  p~= 0.490     normal             
  [Low16/64]BRank(12):1536(1)       R=  +0.4  p~= 0.366     normal             
  [Low16/64]BRank(12):2K(2)         R=  -1.0  p~= 0.744     normal             
  [Low16/64]BRank(12):3K(1)         R=  -0.7  p~= 0.689     normal             
  [Low16/64]BRank(12):4K(2)         R=  -0.2  p~= 0.554     normal             
  [Low16/64]BRank(12):6K(1)         R=  -0.7  p~= 0.689     normal             
  [Low16/64]BRank(12):8K(1)         R=  -0.7  p~= 0.689     normal             
  [Low16/64]mod3n(5):(0,9-0)        R=  -0.1  p = 0.522     normal             
  [Low16/64]mod3n(5):(1,9-0)        R=  +2.9  p = 0.077     normal             
  [Low16/64]mod3n(5):(2,9-0)        R=  +2.6  p = 0.103     normal             
  [Low16/64]mod3n(5):(3,9-0)        R=  +1.6  p = 0.221     normal             
  [Low16/64]mod3n(5):(4,9-0)        R=  -1.1  p = 0.705     normal             
  [Low16/64]mod3n(5):(5,9-0)        R=  -3.9  p = 0.974     normal             
  [Low16/64]mod3n(5):(6,9-0)        R=  -1.7  p = 0.805     normal             
  [Low16/64]mod3n(5):(7,9-1)        R=  -1.0  p = 0.687     normal             
  [Low16/64]mod3n(5):(8,9-1)        R=  +1.2  p = 0.281     normal             
  [Low16/64]mod3n(5):(9,9-2)        R=  -2.3  p = 0.881     normal             
  [Low16/64]mod3n(5):(10,9-2)       R=  -3.2  p = 0.950     normal             
  [Low16/64]mod3n(5):(11,9-3)       R=  +1.2  p = 0.261     normal             
  [Low16/64]mod3n(5):(12,9-3)       R=  -0.6  p = 0.603     normal             
  [Low16/64]mod3n(5):(13,9-4)       R=  -1.6  p = 0.789     normal             
  [Low16/64]mod3n(5):(14,9-4)       R=  -0.2  p = 0.523     normal             
  [Low16/64]mod3n(5):(15,9-5)       R=  -0.8  p = 0.635     normal             
  [Low16/64]TMFn(2+0):wl            R=  +1.2  p~= 0.3       normal             
  [Low16/64]TMFn(2+1):wl            R=  -0.4  p~= 0.6       normal             
  [Low16/64]TMFn(2+2):wl            R=  -1.4  p~= 0.7       normal             
  [Low16/64]TMFn(2+3):wl            R=  -0.1  p~= 0.5       normal             
  [Low16/64]TMFn(2+4):wl            R=  +0.4  p~= 0.4       normal             
  [Low16/64]TMFn(2+5):wl            R=  -1.3  p~= 0.7       normal             
  [Low16/64]TMFn(2+6):wl            R=  -0.6  p~= 0.6       normal             
  [Low16/64]TMFn(2+7):wl            R=  -2.0  p~= 0.8       normal             
  [Low16/64]TMFn(2+8):wl            R=  +0.7  p~= 0.4       normal             
  [Low16/64]TMFn(2+9):wl            R=  -1.4  p~= 0.7       normal             
  [Low16/64]TMFn(2+10):wl           R=  -3.5  p~= 0.9       normal             
  [Low4/64]BCFN(2+0,13-0,T)         R=  +4.4  p = 0.037     normal             
  [Low4/64]BCFN(2+1,13-0,T)         R=  +1.1  p = 0.319     normal             
  [Low4/64]BCFN(2+2,13-0,T)         R=  -1.5  p = 0.724     normal             
  [Low4/64]BCFN(2+3,13-0,T)         R=  +1.7  p = 0.248     normal             
  [Low4/64]BCFN(2+4,13-0,T)         R=  +0.2  p = 0.456     normal             
  [Low4/64]BCFN(2+5,13-1,T)         R=  +1.1  p = 0.321     normal             
  [Low4/64]BCFN(2+6,13-1,T)         R=  +0.1  p = 0.471     normal             
  [Low4/64]BCFN(2+7,13-2,T)         R=  -1.3  p = 0.698     normal             
  [Low4/64]BCFN(2+8,13-3,T)         R=  +2.7  p = 0.136     normal             
  [Low4/64]BCFN(2+9,13-3,T)         R=  -1.1  p = 0.657     normal             
  [Low4/64]BCFN(2+10,13-4,T)        R=  +0.1  p = 0.463     normal             
  [Low4/64]BCFN(2+11,13-5,T)        R=  -0.3  p = 0.531     normal             
  [Low4/64]BCFN(2+12,13-5,T)        R=  +4.0  p = 0.058     normal             
  [Low4/64]BCFN(2+13,13-6,T)        R=  +0.9  p = 0.319     normal             
  [Low4/64]BCFN(2+14,13-6,T)        R=  -3.1  p = 0.919     normal             
  [Low4/64]BCFN(2+15,13-7,T)        R=  +3.0  p = 0.106     normal             
  [Low4/64]BCFN(2+16,13-8,T)        R=  +0.1  p = 0.415     normal             
  [Low4/64]BCFN(2+17,13-8,T)        R=  -0.0  p = 0.442     normal             
  [Low4/64]BCFN(2+18,13-9,T)        R=  +0.7  p = 0.305     normal             
  [Low4/64]BCFN(2+19,13-9,T)        R=  +0.4  p = 0.341     normal             
  [Low4/64]DC6-9x1Bytes-1           R=  -2.6  p = 0.946     normal             
  [Low4/64]Gap-16:A                 R=  -0.9  p = 0.784     normal             
  [Low4/64]Gap-16:B                 R=  -2.0  p = 0.916     normal             
  [Low4/64]FPF-14+6/16:(0,14-0)     R=  -0.9  p = 0.739     normal             
  [Low4/64]FPF-14+6/16:(1,14-0)     R=  -0.8  p = 0.710     normal             
  [Low4/64]FPF-14+6/16:(2,14-0)     R=  -1.1  p = 0.791     normal             
  [Low4/64]FPF-14+6/16:(3,14-0)     R=  +2.1  p = 0.068     normal             
  [Low4/64]FPF-14+6/16:(4,14-0)     R=  +2.5  p = 0.039     normal             
  [Low4/64]FPF-14+6/16:(5,14-0)     R=  -0.5  p = 0.635     normal             
  [Low4/64]FPF-14+6/16:(6,14-0)     R=  -2.0  p = 0.923     normal             
  [Low4/64]FPF-14+6/16:(7,14-0)     R=  -0.1  p = 0.527     normal             
  [Low4/64]FPF-14+6/16:(8,14-0)     R=  +1.3  p = 0.175     normal             
  [Low4/64]FPF-14+6/16:(9,14-1)     R=  +2.2  p = 0.060     normal             
  [Low4/64]FPF-14+6/16:(10,14-2)    R=  +0.3  p = 0.426     normal             
  [Low4/64]FPF-14+6/16:(11,14-2)    R=  +1.9  p = 0.089     normal             
  [Low4/64]FPF-14+6/16:(12,14-3)    R=  -0.5  p = 0.648     normal             
  [Low4/64]FPF-14+6/16:(13,14-4)    R=  -3.8  p =1-2.2e-3   normal             
  [Low4/64]FPF-14+6/16:(14,14-5)    R=  -0.7  p = 0.694     normal             
  [Low4/64]FPF-14+6/16:(15,14-5)    R=  +0.5  p = 0.367     normal             
  [Low4/64]FPF-14+6/16:(16,14-6)    R=  -1.0  p = 0.759     normal             
  [Low4/64]FPF-14+6/16:(17,14-7)    R=  -1.4  p = 0.833     normal             
  [Low4/64]FPF-14+6/16:(18,14-8)    R=  +0.1  p = 0.455     normal             
  [Low4/64]FPF-14+6/16:(19,14-8)    R=  +2.7  p = 0.037     normal             
  [Low4/64]FPF-14+6/16:(20,14-9)    R=  -0.2  p = 0.530     normal             
  [Low4/64]FPF-14+6/16:(21,14-10)   R=  -0.2  p = 0.498     normal             
  [Low4/64]FPF-14+6/16:(22,14-11)   R=  +1.6  p = 0.135     normal             
  [Low4/64]FPF-14+6/16:(23,14-11)   R=  -1.1  p = 0.767     normal             
  [Low4/64]FPF-14+6/16:all          R=  +0.6  p = 0.355     normal             
  [Low4/64]FPF-14+6/16:cross        R=  +0.8  p = 0.211     normal             
  [Low4/64]BRank(12):128(8)         R=  -0.7  p~= 0.670     normal             
  [Low4/64]BRank(12):256(8)         R=  -1.5  p~= 0.940     normal             
  [Low4/64]BRank(12):384(2)         R=  -1.0  p~= 0.744     normal             
  [Low4/64]BRank(12):512(4)         R=  -0.2  p~= 0.500     normal             
  [Low4/64]BRank(12):768(1)         R=  -0.7  p~= 0.689     normal             
  [Low4/64]BRank(12):1K(4)          R=  -1.4  p~= 0.890     normal             
  [Low4/64]BRank(12):1536(1)        R=  -0.7  p~= 0.689     normal             
  [Low4/64]BRank(12):2K(2)          R=  -0.2  p~= 0.554     normal             
  [Low4/64]BRank(12):3K(1)          R=  +0.4  p~= 0.366     normal             
  [Low4/64]BRank(12):4K(2)          R=  +0.6  p~= 0.322     normal             
  [Low4/64]BRank(12):6K(1)          R=  -0.7  p~= 0.689     normal             
  [Low4/64]mod3n(5):(0,9-0)         R=  -0.4  p = 0.579     normal             
  [Low4/64]mod3n(5):(1,9-0)         R=  +0.8  p = 0.344     normal             
  [Low4/64]mod3n(5):(2,9-0)         R=  -0.7  p = 0.636     normal             
  [Low4/64]mod3n(5):(3,9-0)         R=  -1.3  p = 0.742     normal             
  [Low4/64]mod3n(5):(4,9-0)         R=  -2.7  p = 0.912     normal             
  [Low4/64]mod3n(5):(5,9-1)         R=  -0.1  p = 0.514     normal             
  [Low4/64]mod3n(5):(6,9-1)         R=  +1.4  p = 0.248     normal             
  [Low4/64]mod3n(5):(7,9-2)         R=  -0.4  p = 0.572     normal             
  [Low4/64]mod3n(5):(8,9-2)         R=  -0.7  p = 0.624     normal             
  [Low4/64]mod3n(5):(9,9-3)         R=  +0.2  p = 0.452     normal             
  [Low4/64]mod3n(5):(10,9-3)        R=  +0.2  p = 0.456     normal             
  [Low4/64]mod3n(5):(11,9-4)        R=  -4.4  p =1-5.9e-3   normal             
  [Low4/64]mod3n(5):(12,9-4)        R=  -2.4  p = 0.893     normal             
  [Low4/64]mod3n(5):(13,9-5)        R=  -0.0  p = 0.470     normal             
  [Low4/64]mod3n(5):(14,9-5)        R=  -2.7  p = 0.939     normal             
  [Low4/64]mod3n(5):(15,9-6)        R=  -1.2  p = 0.725     normal             
  [Low4/64]TMFn(2+0):wl             R=  -2.2  p~= 0.8       normal             
  [Low4/64]TMFn(2+1):wl             R=  -2.7  p~= 0.8       normal             
  [Low4/64]TMFn(2+2):wl             R=  -0.2  p~= 0.5       normal             
  [Low4/64]TMFn(2+3):wl             R=  +1.7  p~= 0.3       normal             
  [Low4/64]TMFn(2+4):wl             R=  +0.2  p~= 0.5       normal             
  [Low4/64]TMFn(2+5):wl             R=  +0.6  p~= 0.4       normal             
  [Low4/64]TMFn(2+6):wl             R=  +0.0  p~= 0.5       normal             
  [Low4/64]TMFn(2+7):wl             R=  +0.3  p~= 0.5       normal             
  [Low4/64]TMFn(2+8):wl             R=  +0.5  p~= 0.4       normal             
  [Low1/64]BCFN(2+0,13-0,T)         R=  -0.5  p = 0.571     normal             
  [Low1/64]BCFN(2+1,13-0,T)         R=  +3.8  p = 0.063     normal             
  [Low1/64]BCFN(2+2,13-1,T)         R=  +1.3  p = 0.299     normal             
  [Low1/64]BCFN(2+3,13-1,T)         R=  -1.1  p = 0.675     normal             
  [Low1/64]BCFN(2+4,13-1,T)         R=  +0.3  p = 0.447     normal             
  [Low1/64]BCFN(2+5,13-2,T)         R=  -2.7  p = 0.866     normal             
  [Low1/64]BCFN(2+6,13-3,T)         R=  +2.5  p = 0.153     normal             
  [Low1/64]BCFN(2+7,13-3,T)         R=  +1.5  p = 0.258     normal             
  [Low1/64]BCFN(2+8,13-4,T)         R=  +1.4  p = 0.277     normal             
  [Low1/64]BCFN(2+9,13-5,T)         R=  +0.2  p = 0.432     normal             
  [Low1/64]BCFN(2+10,13-5,T)        R=  +2.6  p = 0.139     normal             
  [Low1/64]BCFN(2+11,13-6,T)        R=  -1.3  p = 0.686     normal             
  [Low1/64]BCFN(2+12,13-6,T)        R=  +2.1  p = 0.184     normal             
  [Low1/64]BCFN(2+13,13-7,T)        R=  -1.4  p = 0.705     normal             
  [Low1/64]BCFN(2+14,13-8,T)        R=  -1.9  p = 0.787     normal             
  [Low1/64]BCFN(2+15,13-8,T)        R=  -3.1  p = 0.952     normal             
  [Low1/64]BCFN(2+16,13-9,T)        R=  +0.9  p = 0.278     normal             
  [Low1/64]BCFN(2+17,13-9,T)        R=  -1.1  p = 0.648     normal             
  [Low1/64]DC6-9x1Bytes-1           R=  -1.4  p = 0.850     normal             
  [Low1/64]Gap-16:A                 R=  +1.5  p = 0.223     normal             
  [Low1/64]Gap-16:B                 R=  +1.3  p = 0.187     normal             
  [Low1/64]FPF-14+6/16:(0,14-0)     R=  -0.6  p = 0.668     normal             
  [Low1/64]FPF-14+6/16:(1,14-0)     R=  +0.2  p = 0.445     normal             
  [Low1/64]FPF-14+6/16:(2,14-0)     R=  -2.2  p = 0.941     normal             
  [Low1/64]FPF-14+6/16:(3,14-0)     R=  +2.3  p = 0.054     normal             
  [Low1/64]FPF-14+6/16:(4,14-0)     R=  -2.1  p = 0.936     normal             
  [Low1/64]FPF-14+6/16:(5,14-0)     R=  -0.4  p = 0.620     normal             
  [Low1/64]FPF-14+6/16:(6,14-0)     R=  +1.2  p = 0.211     normal             
  [Low1/64]FPF-14+6/16:(7,14-1)     R=  -1.9  p = 0.916     normal             
  [Low1/64]FPF-14+6/16:(8,14-2)     R=  +0.3  p = 0.406     normal             
  [Low1/64]FPF-14+6/16:(9,14-2)     R=  -0.6  p = 0.659     normal             
  [Low1/64]FPF-14+6/16:(10,14-3)    R=  -3.9  p =1-2.4e-3   normal             
  [Low1/64]FPF-14+6/16:(11,14-4)    R=  +1.1  p = 0.224     normal             
  [Low1/64]FPF-14+6/16:(12,14-5)    R=  +0.7  p = 0.314     normal             
  [Low1/64]FPF-14+6/16:(13,14-5)    R=  +1.3  p = 0.176     normal             
  [Low1/64]FPF-14+6/16:(14,14-6)    R=  -1.6  p = 0.879     normal             
  [Low1/64]FPF-14+6/16:(15,14-7)    R=  +0.7  p = 0.311     normal             
  [Low1/64]FPF-14+6/16:(16,14-8)    R=  +0.2  p = 0.424     normal             
  [Low1/64]FPF-14+6/16:(17,14-8)    R=  +0.9  p = 0.245     normal             
  [Low1/64]FPF-14+6/16:(18,14-9)    R=  +0.1  p = 0.447     normal             
  [Low1/64]FPF-14+6/16:(19,14-10)   R=  +2.1  p = 0.081     normal             
  [Low1/64]FPF-14+6/16:(20,14-11)   R=  +2.2  p = 0.077     normal             
  [Low1/64]FPF-14+6/16:(21,14-11)   R=  -1.6  p = 0.898     normal             
  [Low1/64]FPF-14+6/16:all          R=  -1.4  p = 0.841     normal             
  [Low1/64]FPF-14+6/16:cross        R=  +0.8  p = 0.196     normal             
  [Low1/64]BRank(12):128(8)         R=  +2.5  p~= 0.020     normal             
  [Low1/64]BRank(12):256(4)         R=  +0.4  p~= 0.340     normal             
  [Low1/64]BRank(12):384(2)         R=  -0.2  p~= 0.554     normal             
  [Low1/64]BRank(12):512(4)         R=  -0.2  p~= 0.500     normal             
  [Low1/64]BRank(12):768(1)         R=  -0.7  p~= 0.689     normal             
  [Low1/64]BRank(12):1K(2)          R=  +6.2  p~=  6.7e-3   normal             
  [Low1/64]BRank(12):1536(1)        R=  -0.7  p~= 0.689     normal             
  [Low1/64]BRank(12):2K(2)          R=  -0.2  p~= 0.554     normal             
  [Low1/64]BRank(12):3K(1)          R=  +0.4  p~= 0.366     normal             
  [Low1/64]BRank(12):4K(1)          R=  +0.4  p~= 0.366     normal             
  [Low1/64]mod3n(5):(0,9-0)         R=  +2.3  p = 0.128     normal             
  [Low1/64]mod3n(5):(1,9-0)         R=  -1.6  p = 0.796     normal             
  [Low1/64]mod3n(5):(2,9-0)         R=  -3.9  p = 0.974     normal             
  [Low1/64]mod3n(5):(3,9-1)         R=  -1.0  p = 0.687     normal             
  [Low1/64]mod3n(5):(4,9-1)         R=  +5.6  p =  3.0e-3   normal             
  [Low1/64]mod3n(5):(5,9-2)         R=  +4.1  p = 0.025     normal             
  [Low1/64]mod3n(5):(6,9-2)         R=  +1.8  p = 0.183     normal             
  [Low1/64]mod3n(5):(7,9-3)         R=  +2.0  p = 0.154     normal             
  [Low1/64]mod3n(5):(8,9-3)         R=  +1.4  p = 0.244     normal             
  [Low1/64]mod3n(5):(9,9-4)         R=  +0.3  p = 0.430     normal             
  [Low1/64]mod3n(5):(10,9-4)        R=  +0.2  p = 0.444     normal             
  [Low1/64]mod3n(5):(11,9-5)        R=  -3.5  p = 0.981     normal             
  [Low1/64]mod3n(5):(12,9-5)        R=  -2.0  p = 0.850     normal             
  [Low1/64]mod3n(5):(13,9-6)        R=  +3.4  p = 0.055     normal             
  [Low1/64]mod3n(5):(14,9-6)        R=  -1.1  p = 0.687     normal             
  [Low1/64]mod3n(5):(15,9-6)        R=  -0.9  p = 0.644     normal             
  [Low1/64]TMFn(2+0):wl             R=  +0.5  p~= 0.4       normal             
  [Low1/64]TMFn(2+1):wl             R=  +0.3  p~= 0.4       normal             
  [Low1/64]TMFn(2+2):wl             R=  -0.6  p~= 0.6       normal             
  [Low1/64]TMFn(2+3):wl             R=  +2.4  p~= 0.2       normal             
  [Low1/64]TMFn(2+4):wl             R=  +0.3  p~= 0.5       normal             
  [Low1/64]TMFn(2+5):wl             R=  -0.2  p~= 0.5       normal             
  [Low1/64]TMFn(2+6):wl             R=  +2.3  p~= 0.2       normal             
```  
  
dieharder results:  
```  
#            dieharder version 3.31.1 Copyright 2003 Robert G. Brown          #  
#=============================================================================#  
   rng_name    |rands/second|   Seed   |  
stdin_input_raw|  2.17e+07  |1931884644|  
#=============================================================================#  
        test_name   |ntup| tsamples |psamples|  p-value |Assessment  
#=============================================================================#  
   diehard_birthdays|   0|       100|     100|0.99430162|  PASSED    
      diehard_operm5|   0|   1000000|     100|0.04757718|  PASSED    
  diehard_rank_32x32|   0|     40000|     100|0.52916497|  PASSED    
    diehard_rank_6x8|   0|    100000|     100|0.76069615|  PASSED    
   diehard_bitstream|   0|   2097152|     100|0.75878572|  PASSED    
        diehard_opso|   0|   2097152|     100|0.91593073|  PASSED    
        diehard_oqso|   0|   2097152|     100|0.11225322|  PASSED    
         diehard_dna|   0|   2097152|     100|0.14906789|  PASSED    
diehard_count_1s_str|   0|    256000|     100|0.06872809|  PASSED    
diehard_count_1s_byt|   0|    256000|     100|0.70856543|  PASSED    
 diehard_parking_lot|   0|     12000|     100|0.50331801|  PASSED    
    diehard_2dsphere|   2|      8000|     100|0.96539635|  PASSED    
    diehard_3dsphere|   3|      4000|     100|0.74502709|  PASSED    
     diehard_squeeze|   0|    100000|     100|0.23852963|  PASSED    
        diehard_sums|   0|       100|     100|0.15815888|  PASSED    
        diehard_runs|   0|    100000|     100|0.13201443|  PASSED    
        diehard_runs|   0|    100000|     100|0.88888937|  PASSED    
       diehard_craps|   0|    200000|     100|0.53316043|  PASSED    
       diehard_craps|   0|    200000|     100|0.75180320|  PASSED    
 marsaglia_tsang_gcd|   0|  10000000|     100|0.26539375|  PASSED    
 marsaglia_tsang_gcd|   0|  10000000|     100|0.22152737|  PASSED    
         sts_monobit|   1|    100000|     100|0.04424458|  PASSED    
            sts_runs|   2|    100000|     100|0.68065371|  PASSED    
          sts_serial|   1|    100000|     100|0.46910435|  PASSED    
          sts_serial|   2|    100000|     100|0.52431391|  PASSED    
          sts_serial|   3|    100000|     100|0.03910829|  PASSED    
          sts_serial|   3|    100000|     100|0.83463962|  PASSED    
          sts_serial|   4|    100000|     100|0.39571782|  PASSED    
          sts_serial|   4|    100000|     100|0.91867874|  PASSED    
          sts_serial|   5|    100000|     100|0.81372494|  PASSED    
          sts_serial|   5|    100000|     100|0.25715072|  PASSED    
          sts_serial|   6|    100000|     100|0.93572461|  PASSED    
          sts_serial|   6|    100000|     100|0.62219233|  PASSED    
          sts_serial|   7|    100000|     100|0.46727974|  PASSED    
          sts_serial|   7|    100000|     100|0.28496488|  PASSED    
          sts_serial|   8|    100000|     100|0.28559708|  PASSED    
          sts_serial|   8|    100000|     100|0.68100462|  PASSED    
          sts_serial|   9|    100000|     100|0.89784955|  PASSED    
          sts_serial|   9|    100000|     100|0.92024709|  PASSED    
          sts_serial|  10|    100000|     100|0.30951700|  PASSED    
          sts_serial|  10|    100000|     100|0.62662810|  PASSED    
          sts_serial|  11|    100000|     100|0.74469128|  PASSED    
          sts_serial|  11|    100000|     100|0.23608110|  PASSED    
          sts_serial|  12|    100000|     100|0.42035672|  PASSED    
          sts_serial|  12|    100000|     100|0.45049926|  PASSED    
          sts_serial|  13|    100000|     100|0.45518631|  PASSED    
          sts_serial|  13|    100000|     100|0.92120559|  PASSED    
          sts_serial|  14|    100000|     100|0.16234043|  PASSED    
          sts_serial|  14|    100000|     100|0.48662893|  PASSED    
          sts_serial|  15|    100000|     100|0.43036875|  PASSED    
          sts_serial|  15|    100000|     100|0.77014635|  PASSED    
          sts_serial|  16|    100000|     100|0.89116198|  PASSED    
          sts_serial|  16|    100000|     100|0.91698214|  PASSED    
         rgb_bitdist|   1|    100000|     100|0.38829211|  PASSED    
         rgb_bitdist|   2|    100000|     100|0.21485722|  PASSED    
         rgb_bitdist|   3|    100000|     100|0.60808240|  PASSED    
         rgb_bitdist|   4|    100000|     100|0.59569072|  PASSED    
         rgb_bitdist|   5|    100000|     100|0.28092216|  PASSED    
         rgb_bitdist|   6|    100000|     100|0.62120423|  PASSED    
         rgb_bitdist|   7|    100000|     100|0.91433736|  PASSED    
         rgb_bitdist|   8|    100000|     100|0.92235115|  PASSED    
         rgb_bitdist|   9|    100000|     100|0.50837707|  PASSED    
         rgb_bitdist|  10|    100000|     100|0.99918595|   WEAK     
         rgb_bitdist|  10|    100000|     200|0.48784064|  PASSED    
         rgb_bitdist|  11|    100000|     100|0.99487266|  PASSED    
         rgb_bitdist|  12|    100000|     100|0.58779881|  PASSED    
rgb_minimum_distance|   2|     10000|    1000|0.00287616|   WEAK     
rgb_minimum_distance|   2|     10000|    1100|0.00177320|   WEAK     
rgb_minimum_distance|   2|     10000|    1200|0.00968925|  PASSED    
rgb_minimum_distance|   3|     10000|    1000|0.73704757|  PASSED    
rgb_minimum_distance|   4|     10000|    1000|0.52727063|  PASSED    
rgb_minimum_distance|   5|     10000|    1000|0.18510147|  PASSED    
    rgb_permutations|   2|    100000|     100|0.16071502|  PASSED    
    rgb_permutations|   3|    100000|     100|0.30293962|  PASSED    
    rgb_permutations|   4|    100000|     100|0.91548693|  PASSED    
    rgb_permutations|   5|    100000|     100|0.39783101|  PASSED    
      rgb_lagged_sum|   0|   1000000|     100|0.02870332|  PASSED    
      rgb_lagged_sum|   1|   1000000|     100|0.43281658|  PASSED    
      rgb_lagged_sum|   2|   1000000|     100|0.98274161|  PASSED    
      rgb_lagged_sum|   3|   1000000|     100|0.77058329|  PASSED    
      rgb_lagged_sum|   4|   1000000|     100|0.99880381|   WEAK     
      rgb_lagged_sum|   4|   1000000|     200|0.21638250|  PASSED    
      rgb_lagged_sum|   5|   1000000|     100|0.37168954|  PASSED    
      rgb_lagged_sum|   6|   1000000|     100|0.97209059|  PASSED    
      rgb_lagged_sum|   7|   1000000|     100|0.43367713|  PASSED    
      rgb_lagged_sum|   8|   1000000|     100|0.74260603|  PASSED    
      rgb_lagged_sum|   9|   1000000|     100|0.78140109|  PASSED    
      rgb_lagged_sum|  10|   1000000|     100|0.98292662|  PASSED    
      rgb_lagged_sum|  11|   1000000|     100|0.82154394|  PASSED    
      rgb_lagged_sum|  12|   1000000|     100|0.20849471|  PASSED    
      rgb_lagged_sum|  13|   1000000|     100|0.92642594|  PASSED    
      rgb_lagged_sum|  14|   1000000|     100|0.99631994|   WEAK     
      rgb_lagged_sum|  14|   1000000|     200|0.83931360|  PASSED    
      rgb_lagged_sum|  15|   1000000|     100|0.34126322|  PASSED    
      rgb_lagged_sum|  16|   1000000|     100|0.47359329|  PASSED    
      rgb_lagged_sum|  17|   1000000|     100|0.92554728|  PASSED    
      rgb_lagged_sum|  18|   1000000|     100|0.49509094|  PASSED    
      rgb_lagged_sum|  19|   1000000|     100|0.63816541|  PASSED    
      rgb_lagged_sum|  20|   1000000|     100|0.01801710|  PASSED    
      rgb_lagged_sum|  21|   1000000|     100|0.72409747|  PASSED    
      rgb_lagged_sum|  22|   1000000|     100|0.62491755|  PASSED    
      rgb_lagged_sum|  23|   1000000|     100|0.15508390|  PASSED    
      rgb_lagged_sum|  24|   1000000|     100|0.60551867|  PASSED    
      rgb_lagged_sum|  25|   1000000|     100|0.06332862|  PASSED    
      rgb_lagged_sum|  26|   1000000|     100|0.42563602|  PASSED    
      rgb_lagged_sum|  27|   1000000|     100|0.30592275|  PASSED    
      rgb_lagged_sum|  28|   1000000|     100|0.83089700|  PASSED    
      rgb_lagged_sum|  29|   1000000|     100|0.99180544|  PASSED    
      rgb_lagged_sum|  30|   1000000|     100|0.09671590|  PASSED    
      rgb_lagged_sum|  31|   1000000|     100|0.24311798|  PASSED    
      rgb_lagged_sum|  32|   1000000|     100|0.80085804|  PASSED    
     rgb_kstest_test|   0|     10000|    1000|0.34968098|  PASSED    
     dab_bytedistrib|   0|  51200000|       1|0.33037224|  PASSED    
             dab_dct| 256|     50000|       1|0.36491927|  PASSED    
Preparing to run test 207.  ntuple = 0  
        dab_filltree|  32|  15000000|       1|0.80783564|  PASSED    
        dab_filltree|  32|  15000000|       1|0.40280309|  PASSED    
Preparing to run test 208.  ntuple = 0  
       dab_filltree2|   0|   5000000|       1|0.70961220|  PASSED    
       dab_filltree2|   1|   5000000|       1|0.82573038|  PASSED    
Preparing to run test 209.  ntuple = 0  
        dab_monobit2|  12|  65000000|       1|0.17945863|  PASSED    
```  
  
Building practrand:  
* https://sourceforge.net/p/pracrand/bugs/12/  
* https://sourceforge.net/p/pracrand/bugs/11/  
```  
$ cd PractRand_094/  
$ patch -p1 <~/Downloads/fix-unix-make.diff   
patching file src/test_batteries.cpp  
patching file tools/RNG_test.cpp  
$ patch -p1 <~/Downloads/practrand-0.94-fix-printf-format-flags.patch   
patching file src/tests.cpp  
patching file tools/Test_calibration.cpp  
$ patch -p1 <~/Downloads/practrand-0.94-fix-ub-missing-return-value.patch   
patching file tools/RNG_test.cpp  
$ patch -p1 <~/Downloads/practrand-0.94-fix-ub-shift.patch   
patching file src/tests.cpp  
$ g++ -O2 -march=native -fstack-protector-strong src/*.cpp src/RNGs/*.cpp src/RNGs/other/*.cpp -Iinclude -pthread tools/RNG_test.cpp -o RNG_test  
```

---

## Comment 7

> Username: kotika  
> Created at: 2019-09-28 08:32:06 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536166181  

> On Sep 26, 2019, at 13:10, Kostas Savvidis <ksavvidis@gmail.com> wrote:  
>   
> I see three separate issues with your proposal.  
>   
> 1) It is very well established that  subtract_with_carry + discard_block_engine = RANLUX  
> only for the very specific parameter sets described in the paper by Luscher. What you have is not RANLUX.  
> I dont think it is fair to use open-source software names   
> without permission. I guess you could rename it to address this concern.  
>   
> 2) You say it is 40% faster than RANLUX, but RANLUX itself is 10-20 times slower that the other generators.  
> As a result, your generators would still be 5-10 times slower than the others.   
>   
> 3) Your paper is not published in a journal.  I know, it is a little old fashioned in the age of facebook, but it is still a requirement.  
>   
  
  
On 9/26/19 6:02 AM, Nick wrote:  
>> 3) Your paper is not published in a journal. I know, it is a little old fashioned in the age of facebook, but it is still a requirement.  
>   
> And we're talking about standardizing PCG! I think that ship has sailed.  
>   
  
I would still like Christoph Conrads to address the first two points.   
  
Cheers, Kostas

---

## Comment 8

> Username: christoph-conrads  
> Created at: 2019-09-28 11:44:22 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536180626  

> 1) It is very well established that subtract_with_carry + discard_block_engine = RANLUX only for the very specific parameter sets described in the paper by Luscher. What you have is not RANLUX. I dont think it is fair to use open-source software names without permission. I guess you could rename it to address this concern.  
  
Changing parameters is in my opinion not sufficient to warrant renaming the generator. For example quicksort with randomized or three pivots is still called quicksort. The (Francis) QR algorithm is after 50 years of progress in numerical linear algebra and all of its adaptions to modern computer architectures still called the QR algorithm. The proposed generator parameters were derived by applying the methods given in the papers by Marsaglia, Zaman and Lüscher with only minor modifications. I retraced almost all of the steps in their papers. Also, the new parameters preserve all desirable properties of a RANLUX; no corners were cut.  
  
> 2) You say it is 40% faster than RANLUX, but RANLUX itself is 10-20 times slower that the other generators. As a result, your generators would still be 5-10 times slower than the others.  
  
Boost and C++11 ship with RANLUX PRNG implementations. The new 32-bit generator possesses all good properties of the existing generators while being much faster and it can be implemented with a simple type alias. Moreover, in C++ the generator never uses (single-precision) floating-point arithmetic which was originally the intention behind 24-bit numbers (cf. Section 2.4 in Lüscher's paper).

---

## Comment 9

> Username: NAThompson  
> Created at: 2019-09-28 12:32:34 UTC  
> Updated at: 2019-09-28 15:16:11 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536185289  

@christoph-conrads : Your dieharder and practrand results look good; I'll try to reproduce them this afternoon. If people are still unconvinced (not me) you could also try to get TestU01 working.  
  
Do you mind writing a googlebenchmark file and posting the speeds you get? Also, could you add a benchmark for `std::ranlux` and `std::mt1997` so there is a machine-independent comparison?  
  
Here's an example for timing the normal distribution:  
  
```cpp  
#include <benchmark/benchmark.h>  
#include <array>  
#include <random>  
#include <iostream>  
#include <algorithm>  
  
template<class Real>  
void NormalGeneration(benchmark::State& state)  
{  
    std::mt19937 gen(323723);  
    std::normal_distribution<Real> dis(0, 1);  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(dis(gen));  
    }  
}  
BENCHMARK_TEMPLATE(NormalGeneration, double);  
  
BENCHMARK_MAIN();  
```  
  
Is there a 64 bit generator as well as 32 bit?  
  
Does the generator hold internal state? How much? (Sorry, would read your code to back this out but simply asking seemed faster.)  
  
Is this only recommended for simulations?  
  
Are all 32-bit integers provably in the codomain of the generator?  
  
Can you also verify that the implementation is clean with `-fsanitize=address -fsanitize=undefined`?

---

## Comment 10

> Username: kotika  
> Created at: 2019-09-28 18:22:11 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536213182  

> On Sep 28, 2019, at 14:44, Christoph Conrads <notifications@github.com> wrote:  
> I retraced almost all of the steps in their papers.   
  
   In the field of RNGs every new guy who came and suggested a new LCG with a new modulus and a new multiplier  
gave it a new name, e.g. "minstd" is the same old modulus of 2³¹ − 1 which had been used for 50 years before and a new multiplier=16807.   
This is what you have done too, made a new modulus and multiplier for an LCG.  As you say yourself, your work does not contain any original idea,   
except for factoring the large non-prime modulus which Marsaglia and Zaman could not do in 1990.  
  
Anyway, if you insist it is indeed a genuine ranlux and if your invention is of practical use, why dont you try to convince Luscher to include your "improvements" to his distribution?   
His package is at http://luscher.web.cern.ch/luscher/ranlux/  
and his work is copyrighted. I presume the name is not in the public domain.  
  
* File ranlux.h  
* Copyright (C) 2019 Martin Luescher  
  
  
The last comment is that Luscher himself has improved the performance of his generators by a factor of 2-4 compared to the naive implementations,  
when you say you improved it by 40% what are you comparing it to? And for those who are not aware there is already another experimental  
new implementations of RANLUX by Sibidanov which are even faster.  
  
Cheers,  
Kostas  
  
============================================================================================  
Institute of Nuclear and Particle Physics  
NCSR Demokritos  
  
https://github.com/kotika/random <https://github.com/kotika/random>  
https://mixmax.hepforge.org <https://mixmax.hepforge.org/>

---

## Comment 11

> Username: NAThompson  
> Created at: 2019-09-28 18:30:20 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536214020  

Hi @kotika : Some of your suggestions are indeed interesting, but they are couched in somewhat inflammatory language which will not help us all move forward in making the library better.  
  
Could you edit your comment to only include technical commentary?

---

## Comment 12

> Username: NAThompson  
> Created at: 2019-09-28 18:42:19 UTC  
> Updated at: 2019-09-28 18:44:08 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536215209  

@kotika : yes, you'll find my email in the git log of boost.math.  
  
Also, I recommend editing your original content instead of duplicating it.

---

## Comment 13

> Username: christoph-conrads  
> Created at: 2019-09-28 20:57:17 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536224865  

> If people are still unconvinced (not me) you could also try to get TestU01 working.  
  
I did, it works (see my blog post from the thread start) but I do not want to post the code calling TestU01 because its license is unclear to me. The website mentions GPLv3 while `TestU01-1.2.3/COPYING` contains a non-commercial-use license.  
  
> Do you mind writing a googlebenchmark file and posting the speeds you get? Also, could you add a benchmark for std::ranlux and std::mt1997 so there is a machine-independent comparison?  
  
The file is below and it benchmarks `ranlux32` as well as the Boost.Random implementations of `mt19937`, `ranlux24`, and `ranlux48`. Please not that currently `ranlux32` does not work with the GNU C++ Standard Library (libstdc++) due to [GCC bug 89979](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=89979).  
  
Results:  
  
```sh  
$ ./benchmark-random  
2019-09-28 15:36:56  
Running ./benchmark-random  
Run on (16 X 3600 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x8)  
  L1 Instruction 32K (x8)  
  L2 Unified 512K (x8)  
  L3 Unified 16384K (x2)  
Load Average: 2.15, 2.12, 1.46  
----------------------------------------------------------------  
Benchmark                      Time             CPU   Iterations  
----------------------------------------------------------------  
BM_draw<dummy_engine>      0.233 ns        0.233 ns   1000000000  
BM_draw<br::ranlux24>       44.7 ns         44.7 ns     15584015  
BM_draw<ranlux32>           28.6 ns         28.6 ns     24338008  
BM_draw<br::ranlux48>        168 ns          168 ns      4133434  
BM_draw<br::mt19937>        1.14 ns         1.14 ns    616261343  
```  
  
> Is there a 64 bit generator as well as 32 bit?  
  
Yes but the 64-bit generator requires a small amount of new code in `subtract_with_carry_engine` because the generator recurrence changes from `x[n] = x[s]-x[r]+c` to `x[n] = x[r]-x[s]+c` (note the order of the indices). In my tests, the 64-bit generator is on a 64-bit CPU slightly slower than `ranlux32` but has higher throughput (more bytes per second).  
  
> Does the generator hold internal state? How much? (Sorry, would read your code to back this out but simply asking seemed faster.)  
  
18 32-bit integers for `ranlux32`, 63 64-bit integers for `ranlux64`. For  
comparison, `ranlux24` requires 25 32-bit integers, `mt19937` and `mt19937_64` have 624 32-bit integers of state.  
  
> Is this only recommended for simulations?  
  
The generator aims to create high quality uniform variates. Existing RANLUX flavors have been used successfully in, e.g., computational physics.  
  
> Are all 32-bit integers provably in the codomain of the generator?  
  
Yes, all 32-bit integers are provably in the codomain of `ranlux32`.  
  
Yes, all 64-bit integers are provably in the codomain of `ranlux64`.  
  
> Can you also verify that the implementation is clean with -fsanitize=address -fsanitize=undefined?  
  
Note that the 32-bit generator is just an alias, there is no new code:  
```c++  
  namespace br = boost::random;  
  
  typedef  
    br::subtract_with_carry_engine<boost::uint32_t,32,3,17>  
    ranlux32_base;  
  typedef  
    br::discard_block_engine<ranlux32_base, 389, 16>  
    ranlux32;  
```  
Earlier in this thread, I published `print-random.cpp`. I recompiled it with the following options and let it run for 90 minutes without error:  
* `g++-8 -Wextra -Wall -std=c++98 -pedantic -O2 -march=native print-random.cpp -o print-random-asan -fstack-protector-all -fsanitize=address`  
* `g++-8 -Wextra -Wall -std=c++98 -pedantic -O2 -march=native print-random.cpp -o print-random-ubsan -fstack-protector-all -fsanitize=undefined`  
  
  
Google Benchmark file (`benchmark-random.cpp`):  
```c++  
#include <benchmark/benchmark.h>  
#include <boost/cstdint.hpp>  
#include <boost/random.hpp>  
#include <cassert>  
#include <cstdio>  
#include <limits>  
#include <string>  
  
namespace br = boost::random;  
  
typedef  
    br::subtract_with_carry_engine<boost::uint32_t,32,3,17>  
    ranlux32_base;  
typedef br::discard_block_engine<ranlux32_base, 389, 16> ranlux32;  
  
struct dummy_engine  
{  
  typedef boost::uint32_t result_type;  
  
  static result_type max()  
  { return std::numeric_limits<result_type>::max(); }  
  static result_type min()  
  { return std::numeric_limits<result_type>::min(); }  
  
  result_type operator() ();  
  
  result_type state_ = 0;  
};  
  
dummy_engine::result_type dummy_engine::operator() ()  
{  
    return state_++;  
}  
  
std::string get_name(const dummy_engine&) { return "dummy-prng"; }  
std::string get_name(const br::ranlux24&) { return "ranlux24"; }  
std::string get_name(const br::ranlux48&) { return "ranlux48"; }  
std::string get_name(const ranlux32&) { return "ranlux32";}  
  
template<typename Generator>  
typename Generator::result_type  
draw(Generator& gen) __attribute__((noinline));  
  
template<typename Generator>  
typename Generator::result_type draw(Generator& gen)  
{  
  return gen();  
}  
  
template<typename Generator>  
void BM_draw(benchmark::State& state)  
{  
  Generator gen;  
  
  for(auto _ : state) {  
	  benchmark::DoNotOptimize(gen());  
  }  
}  
  
BENCHMARK_TEMPLATE(BM_draw, dummy_engine);  
BENCHMARK_TEMPLATE(BM_draw, br::ranlux24);  
BENCHMARK_TEMPLATE(BM_draw, ranlux32);  
BENCHMARK_TEMPLATE(BM_draw, br::ranlux48);  
BENCHMARK_TEMPLATE(BM_draw, br::mt19937);  
BENCHMARK_MAIN();  
```  
  
Build instructions for `benchmark-random.cpp`:  
```sh  
$ g++ -Wextra -Wall -std=c++11 -pedantic -O3 -DNDEBUG -march=native benchmark-random.cpp -Iinclude -Llib -lbenchmark -pthread -o benchmark-random  
$ g++ --version  
g++ (Ubuntu 7.4.0-1ubuntu1~18.04.1) 7.4.0  
```

---

## Comment 14

> Username: christoph-conrads  
> Created at: 2019-09-28 21:00:03 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536225056  

> In the field of RNGs every new guy who came and suggested a new LCG with a new modulus and a new multiplier gave it a new name, e.g. "minstd" is the same old modulus of 2³¹ − 1 which had been used for 50 years before and a new multiplier=16807. This is what you have done too, made a new modulus and multiplier for an LCG.  
  
The paper of Marsaglia of Zaman contains multiple tables of generator parameters so I would not say that this is true in general.  
  
> As you say yourself, your work does not contain any original idea, except for factoring the large non-prime modulus which Marsaglia and Zaman could not do in 1990.  
  
* I never said my work would contain no original ideas.  
* There are original ideas in the work.  
  
> Anyway, if you insist it is indeed a genuine ranlux and if your invention is of practical use, why dont you try to convince Luscher to include your "improvements" to his distribution? His package is at http://luscher.web.cern.ch/luscher/ranlux/ and his work is copyrighted. I presume the name is not in the public domain.  
  
You are mixing up copyright and trademark law. Also, strictly speaking Frederick James would be the trademark owner because he introduced the term, not Lüscher.  
  
I would like to publish my findings in Boost.Random.  
  
> when you say you improved it by 40% what are you comparing it to?  
  
If you run the benchmark in the initial post, the two fastest generators should be `br::ranlux24` and `ranlux32`. On my computer, there is a 43% CPU time difference in favor of `ranlux32` (4.57s vs 2.62s). Note that this comparison ignores the higher throughput of `ranlux32`. Earlier I ran similar experiments for my blog post on x86-64 and Raspberry Pi and I feel confident enough to claim that this is an achievable performance difference for many CPU architectures.  
  
With Google Benchmark (see above), the difference is 36% (44.7ns vs 28.6ns) but their measurement setup is different from mine.

---

## Comment 15

> Username: degski  
> Created at: 2019-09-30 05:39:45 UTC  
> Updated at: 2019-09-30 13:05:20 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536408936  

@christoph-conrads   
@NAThompson   
  
>     * Practrand results (64G, 128G).  
  
This is by no means enough testing, think many TB's, not GB's. Basically you'll have to let it run until it fails, or until it starts to look it won't fail, but this can be way out! You should also just get the summary results. See also http://www.pcg-random.org/posts/pcg-passes-practrand.html .  
  
**There is a flaw in your testing.**  
  
    Below you find for the 32-bit generator  
    ...  
    RNG_test using PractRand version 0.94  
    RNG = RNG_stdin64, seed   
  
**The above says you're testing a 32-bit prng on 64-bit input, this means that to generate 1 input, you use 2 outputs of the generator.** This will obviously give good results.  
  
You should run the test like so:  
  
    practrand-tester.exe | RNG_test.exe stdin32 -tlmaxonly   
  
The bench-marks:  
  
    BM_draw<dummy_engine>      0.233 ns        0.233 ns   1000000000  
    BM_draw<br::ranlux24>       44.7 ns         44.7 ns     15584015  
    BM_draw<ranlux32>           28.6 ns         28.6 ns     24338008  
    BM_draw<br::ranlux48>        168 ns          168 ns      4133434  
    BM_draw<br::mt19937>        1.14 ns         1.14 ns    616261343  
  
You do see, that `ranlux32` is 25 times slower than `mt19937` don't you? `mt19937` is [not particularly fast](https://github.com/degski/xoroshiro128plusxoshi#speed-test-google-benchmark-results) and still this faster `ranlux` is 25 times slower.   
  
There are much faster prng's that dont' fail Practrand quickly, like: https://gist.github.com/imneme/85cff47d4bad8de6bdeb671f9c76c814 , https://gist.github.com/imneme/f1f7821f07cf76504a97f6537c818083 and https://gist.github.com/imneme/6179748664e88ef3c34860f44309fc71. The latter, `splitmix`, is very fast, but is a bijection, so it fails the birth-day paradox, it never repeats over its period.  
  
The fastest of them all is https://gist.github.com/imneme/aeae7628565f15fb3fef54be8533e39c , but it [fails `Practrand` at 64GB](https://github.com/degski/xoroshiro128plusxoshi#mcg128_fast).

---

## Comment 16

> Username: NAThompson  
> Created at: 2019-09-30 12:32:16 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536539857  

@degski : Nice catch, yes terabytes is much more appropriate especially since this generator carries enough internal state to make it sensible.

---

## Comment 17

> Username: degski  
> Created at: 2019-09-30 12:44:45 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536544238  

@NAThompson It seems `std::ranlux24` bails out at 2TB, I'm testing at the moment, will take time.

---

## Comment 18

> Username: NAThompson  
> Created at: 2019-09-30 12:59:22 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536549368  

@degski : I don't know how much internal state ranlux24 carries, but could you be seeing a birthday paradox?

---

## Comment 19

> Username: degski  
> Created at: 2019-09-30 13:20:09 UTC  
> Updated at: 2019-09-30 13:26:24 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536557208  

@NAThompson I believe it has a state of  120 bytes. The bdp-problem related only to `splitmix64`, as it's a bijection, which simple means there are no repeats over its period. I doubt that with a 2^64 length period, that that causes any noticeable issues, it just does not pass the bar of 'random' as there, one would expect repeats. The so-called `truncated splitmix64`, generating 32-bit numbers, does not have this problem, but you'll have to generate 2 numbers for 1 64-bit output.  
  
I think for now the only issue is whether `ranlux32` holds up at least as good as `std::ranlux24`. `ranlux64` is different, though, as now we are changing the mechanics, testing will bring it all out.  
  
It still leaves the, for most people, most important issue on the table, 'it's slow' and there are better and faster prng's.

---

## Comment 20

> Username: degski  
> Created at: 2019-09-30 13:46:39 UTC  
> Updated at: 2019-09-30 13:54:43 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536568309  

@NAThompson I've got [code](https://github.com/degski/xoroshiro/blob/master/xoroshiro/xoroshiro.hpp) for a number of `xoroshiro` generators and `splitmix64`. This code has been reviewed by @swatanabe and updated according to his observations/comments. I dropped the 'contribution' coz I would need to supply docs and tests [Steven raised that issue after the code was finished]. The effort of getting into boost-documentation- and testing-infra was at that time too much effort for me. Iff you want to improve `boost::random`, I'm happy for you to take the code and push it forward.

---

## Comment 21

> Username: NAThompson  
> Created at: 2019-09-30 14:00:57 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536574921  

@degski : I'm too busy at the moment to take over any rng efforts. However, I do have time to review contributions.  
  
Your xorshiro code looks incredibly well-done, and even though there might be better RNGs, I think adding your implementation would be a step in the right direction.

---

## Comment 22

> Username: degski  
> Created at: 2019-09-30 14:14:17 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-536580914  

@NAThompson Like I wrote, Steven reviewed it, so it should be good. The problem is in the tests and docs, let's just keep it on the back-burner for now, the code is there and will stay there.

---

## Comment 23

> Username: degski  
> Created at: 2019-10-02 06:03:13 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-537349511  

> It seems std::ranlux24 bails out at 2TB, I'm testing at the moment, will take time.  
  
It [`ranlux32`] passed 2TB, so it looks good. Up to 4TB (only 140'000 secs to go).

---

## Comment 24

> Username: christoph-conrads  
> Created at: 2019-10-02 20:25:35 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-537665244  

> **There is a flaw in your testing.**  
>   
> ```  
> Below you find for the 32-bit generator  
> ...  
> RNG_test using PractRand version 0.94  
> RNG = RNG_stdin64, seed   
> ```  
>   
> **The above says you're testing a 32-bit prng on 64-bit input, this means that to generate 1 input, you use 2 outputs of the generator.** This will obviously give good results.  
  
Thanks for finding this.  
  
> You should run the test like so:  
>   
> ```  
> practrand-tester.exe | RNG_test.exe stdin32 -tlmaxonly   
> ```  
>   
> The bench-marks:  
>   
> ```  
> BM_draw<dummy_engine>      0.233 ns        0.233 ns   1000000000  
> BM_draw<br::ranlux24>       44.7 ns         44.7 ns     15584015  
> BM_draw<ranlux32>           28.6 ns         28.6 ns     24338008  
> BM_draw<br::ranlux48>        168 ns          168 ns      4133434  
> BM_draw<br::mt19937>        1.14 ns         1.14 ns    616261343  
> ```  
>   
> You do see, that `ranlux32` is 25 times slower than `mt19937` don't you? `mt19937` is [not particularly fast](https://github.com/degski/xoroshiro128plusxoshi#speed-test-google-benchmark-results) and still this faster `ranlux` is 25 times slower.  
  
The goal is to provide a better set of parameters for a high-quality PRNG that is _already_ available to all users of Boost.Random and C++11. No or minimal code changes are required, respectively. All `ranlux32` and `ranlux64` do differently from the existing RANLUX flavors is exploiting native integer arithmetic for the cost of a type alias  (almost).  
  
Also, RANLUX may be 25 times slower than the Mersenne Twister but MT is guaranteed to fail empirical tests. It depends on your requirements if this is acceptable to you or not.

---

## Comment 25

> Username: christoph-conrads  
> Created at: 2019-10-02 20:30:30 UTC  
> Updated at: 2019-10-02 20:31:04 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-537667327  

> @degski : I don't know how much internal state ranlux24 carries, but could you be seeing a birthday paradox?  
  
24*24 bits = 576 bits with a period of about 2^568 or 10^171.

---

## Comment 26

> Username: christoph-conrads  
> Created at: 2019-10-02 20:55:53 UTC  
> Updated at: 2019-10-02 21:50:33 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-537676878  

> I think for now the only issue is whether `ranlux32` holds up at least as good as `std::ranlux24`.  
  
It should if the math is correct because `ranlux32` discards a sufficient fraction of variates internally so as to be indistinguishable from a true random generator _in Lüscher's dynamical systems model_. `ranlux24` does not discard enough variates to possess this property.  
  
> `ranlux64` is different, though, as now we are changing the mechanics, testing will bring it all out.  
  
Marsaglia and Zaman discuss in their original paper generators of the form `x[n] = ±x[n-s] ± x[n-r] ± c`. `ranlux64` just picks different signs. Moreover, `ranlux64` has almost 4000 bits of state. Frankly, testing won't do anything except to serve as a basic sanity check.

---

## Comment 27

> Username: christoph-conrads  
> Created at: 2019-10-02 20:57:42 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-537677556  

> > It seems std::ranlux24 bails out at 2TB, I'm testing at the moment, will take time.  
>   
> It [`ranlux32`] passed 2TB, so it looks good. Up to 4TB (only 140'000 secs to go).  
  
Thank you for running the test.

---

## Comment 28

> Username: degski  
> Created at: 2019-10-03 14:22:44 UTC  
> Updated at: 2019-10-03 14:23:29 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-537967753  

>   
>   
> > > It seems std::ranlux24 bails out at 2TB, I'm testing at the moment, will take time.  
> >   
> >   
> > It [`ranlux32`] passed 2TB, so it looks good. Up to 4TB (only 140'000 secs to go).  
>   
> Thank you for running the test.  
  
Unfortunately we [me and my fellow-villagers] had a power-cut [I live in a 3-rd world country] and the test got whacked.  
  
I post the result so far, and do not intend to pursue this any further, this prng is still 25 times slower than MT, so I won't be using it [and nobody should].  
  
    RNG_test using PractRand version 0.94  
    RNG = RNG_stdin32, seed = unknown  
    test set = core, folding = standard (32 bit)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 64 megabytes (2^26 bytes), time= 3.5 seconds  
      no anomalies in 140 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 128 megabytes (2^27 bytes), time= 7.8 seconds  
      no anomalies in 154 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 256 megabytes (2^28 bytes), time= 15.7 seconds  
      Test Name                         Raw       Processed     Evaluation  
      [Low8/32]BCFN(2+2,13-4,T)         R=  -6.8  p =1-6.9e-4   unusual  
      ...and 164 test result(s) without anomalies  
  
    rng=RNG_stdin32, seed=unknown  
    length= 512 megabytes (2^29 bytes), time= 30.6 seconds  
      no anomalies in 178 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 1 gigabyte (2^30 bytes), time= 59.7 seconds  
      no anomalies in 192 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 2 gigabytes (2^31 bytes), time= 117 seconds  
      no anomalies in 204 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 4 gigabytes (2^32 bytes), time= 230 seconds  
      no anomalies in 216 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 8 gigabytes (2^33 bytes), time= 459 seconds  
      no anomalies in 229 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 16 gigabytes (2^34 bytes), time= 915 seconds  
      no anomalies in 240 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 32 gigabytes (2^35 bytes), time= 1817 seconds  
      no anomalies in 251 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 64 gigabytes (2^36 bytes), time= 3683 seconds  
      no anomalies in 263 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 128 gigabytes (2^37 bytes), time= 7542 seconds  
      no anomalies in 273 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 256 gigabytes (2^38 bytes), time= 15129 seconds  
      no anomalies in 284 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 512 gigabytes (2^39 bytes), time= 34261 seconds  
      no anomalies in 295 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 1 terabyte (2^40 bytes), time= 73393 seconds  
      no anomalies in 304 test result(s)  
  
    rng=RNG_stdin32, seed=unknown  
    length= 2 terabytes (2^41 bytes), time= 138845 seconds  
      no anomalies in 313 test result(s)

---

## Comment 29

> Username: christoph-conrads  
> Created at: 2019-10-03 16:35:12 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-538023538  

@swatanabe `ranlux32` is noticeably faster than the existing RANLUX flavors, slightly smaller, and it generates excellent variates. It can be implemented with a simple type alias. There are other competitive PRNGs but unless someone comes along and actually implements them, I suggest to make the most out of the existing code base that is already available to every Boost and every C++11 user.  
  
Are you interested in `ranlux32` and if so, what are your requirements to add it to Boost.Random?  
  
ranlux32 has 544 bits of state plus a carry bit and a period of 2^534.

---

## Comment 30

> Username: christoph-conrads  
> Created at: 2019-10-03 20:21:24 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-538112179  

The reason why Iooked into the RANLUX is because I just could not figure out why someone would voluntarily use a PRNG using 24-bit arithmetic in C++ when 32-bit arithmetic is ubiquitous. This forces compilers to generate an extra instruction (a `div` or a bitwise and) for the computation of the variates. Can we please fix these RANLUX parameters that were chosen for a single-precision floating-point generator because it looks plain ridiculous to use them for integer arithmetic? It looks especially ridiculous in C++, a language aiming to allow efficient use of hardware.

---

## Comment 31

> Username: kotika  
> Created at: 2019-10-03 20:58:29 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-538125824  

> On Sep 28, 2019, at 23:57, Christoph Conrads <notifications@github.com> wrote:  
> Please not that currently ranlux32 does not work with the GNU C++ Standard Library (libstdc++) due to GCC bug 89979.  
>   
  
=============================================================================================================================  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=89979  
  
Reported:	2019-04-04 21:46 UTC by Christoph Conrads   
  
The carry inside `std::subtract_with_carry_engine` is computed incorrectly if word_size == std::numeric_limits<result_type>::digits.   
The bug can be triggered neither by std::ranlux24 nor by std::ranlux48.   
  
Assignee:  	Not yet assigned to anyone   
===============================================================================================================================  
  
The facts:  
  
1) a proposal which can be implemented by adding two lines of typedef  
2) but which does not work with GCC libstdc++  
3) six months later, and noone at GCC has been assigned to look into this  
  
And, today we get another tangible datapoint:  
> On Oct 3, 2019, at 17:22, degski <notifications@github.com> wrote:  
> Unfortunately we had a power-cut [I live in a 3-rd world country] and the test got whacked.  
  
4) the time to run the statistical tests on this "fast" "new" rng is so long that it is longer than the time between power outages in some countries  
  
Cheers,  
Kostas  
  
============================================================================================  
Institute of Nuclear and Particle Physics  
NCSR Demokritos  
  
https://github.com/kotika/random  
https://mixmax.hepforge.org

---

## Comment 32

> Username: christoph-conrads  
> Created at: 2021-08-31 09:36:05 UTC  
> Url: https://github.com/boostorg/random/issues/57#issuecomment-909069946  

> This is by no means enough testing, think many TB's, not GB's. Basically you'll have to let it run until it fails, or until it starts to look it won't fail, but this can be way out! You should also just get the summary results. See also http://www.pcg-random.org/posts/pcg-passes-practrand.html .  
  
I devised new parameters (`b=32`, `r=2`, `s=13`) with the following properties:  
* The PRNG passes Practrand up to at least one petabyte (log below),  
* The PRNG discards _less_ values than `std::ranlux48` (and `std::ranlux48` fails at 4TB).  
* The entire PRNG state fits into one cache line including carry bit without any tricks (e.g., having a single bit for the carry that must be expensively extracted). This does not work with Boost at the moment because the fields are in the wrong order (i.e., the compiler must add padding).  
  
The PRNG was started from the all-zero state with the carry bit set. For the test I made the PRNG regularly print its state so if you want to figure out if I really ran this experiment, you read in a random state in this file and let the PRNG generate approximately 8GB of data before you can again compare with the state in the file.  
  
* Practrand output: [practrand-ranlux32_2_13-20200311.log](https://github.com/boostorg/random/files/7082663/practrand-ranlux32_2_13-20200311.log)  
* PRNG state: [ranlux32_2_13-state-20200311.txt](https://github.com/boostorg/random/files/7082683/ranlux32_2_13-state-20200311.txt)  
* Experiment code: [ranlux32_2_13-experiments-2020-03-11.tar.gz](https://github.com/boostorg/random/files/7082731/ranlux32_2_13-experiments-2020-03-11.tar.gz)  
  
@swatanabe Given that I produce a document explaining how to arrive these parameters and benchmarks, would you consider to accept a pull request for the new parameters?
