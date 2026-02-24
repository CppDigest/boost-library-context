# #61 - uniform_real_distribution<double> produces identical values too often [Open]

> Username: danieltuzes  
> Created at: 2019-11-26 10:12:40 UTC  
> Updated at: 2020-12-30 03:59:00 UTC  
> Url: https://github.com/boostorg/random/issues/61  

If I generate uniformly distributed numbers with `boost::random::uniform_real_distribution<double>` it produces identical values too often. For further information, please read the [original issue on SO](https://stackoverflow.com/questions/59039358/boostrandom-generates-identical-values-too-often-from-the-same-seed-at-differe) and an [example code on wandbox](https://wandbox.org/permlink/ptQg2Jhm3zB3tEtL). I post my problem here as somebody suggested that it could be a bug. I am still not convinced about that but I am also not sure if there is any bug in my code that produces this strange behavior.  
  
I copy the question from SO here but follow the link for further updates and suggestions.  
## Problem description  
Sometimes I get the same random number from a uniform distribution using a Mersenne Twister engine even I properly used the engine and iterated it. I know that the number of possible states of the engine is finite and number of possible generated values is also finite, but this is not the case now.  
  
Using boost's implementation, 1e6 number of uniformly distributed random values are generated on the range [0; 1e7). That means that there way more possible values than required number of random values. However, I get quite often the same values, sometimes more than 100 times in this range. How is it possible?  
  
## Code  
A simple code is provided to reproduce the situation. On both platforms I get the same problem:  
  
* MSVS 2019 with boost-random:x64-windows 1.71.0, and  
* g++ (Ubuntu 5.4.0-6ubuntu1~16.04.12) 5.4.0 20160609 with libboost-dev 1.58.0.1ubuntu1  
  
```cpp  
#include <iostream>  
#include <chrono>  
  
#include <boost/random/mersenne_twister.hpp>          // random number generator  
#include <boost/random/uniform_real_distribution.hpp> // uniform distribution generator  
using namespace std;  
  
int main()  
{  
    size_t seed = static_cast<int> (std::chrono::system_clock::now().time_since_epoch().count());  
    cout << "seed = " << seed << endl;  
      
    boost::random::mt19937 engine(seed);                         // the random number generator engine  
    boost::random::uniform_real_distribution<double> u(0, 1e7);  // uniformly distributed double values on the range [0; 1e7)  
    cout.precision(20);  
    vector<double> history;                                      // stores the generated values for comparison  
    for (size_t i = 0; i < 1e6; ++i)  
    {  
        history.push_back(u(engine));  
        for (size_t j = 0; j < i; ++j)  
            if (history[i] == history[j])  
                cout << "Equal values ("<< history[i] <<") at ID = " << i << " and " << j << endl;  
    }  
}  
```  
## Question  
Is there a bug in the code that produces the same values? Or is it a bug in boost?  
  
For my task it is important to generate numbers with uniform distribution. Finding identical values is one of the easiest tests but there are many more and I am sure I don't want to do quality analysis on a well-known library like Boost. I didn't want to use the standard library, because it is not guaranteed that two different compilers will give the same sequence for the same seed values, but it was a requirement for the task. What kind of a solution can you suggest?  
  
## Note  
A strange behavior can be seen if one compares the generated values with the one `std::random generates`. Example for values from `random::boost` for seed 4561565448989 is   
  
    1755586.0406719148159  
    3354420.976247638464   <--  
    3630764.0071026980877  
    3488445.2889673411846  <--  
    7920481.4555123448372  
    8773544.1024415194988  <--  
  
while standard library generates  
  
    3354420.9766563926823  <--  
    3488445.2898126943037  <--  
    8773544.1042856499553  <--  
    ...  
That is, every second generated value in the boost's sequence is very close to a corresponding value in the standard library's implementation. When two values in the boost-sequence are equal, the values in the standard-library-sequence are not equal, but close to each other. The similarity holds for MSVS and g++ compilers too, which have the right to have different implementation for Mersenne Twister and distributions.  
  
### It is the distribution generator  
If the standard library's MT engine is used, but boost's distribution, the problem persists. But if the engine is the one from boost and the distribution is standard, the problem disappears. The problem is, as [Peter pointed out](https://stackoverflow.com/a/59040191/1837006), that the uniform distribution is platform depend for which I use boost.

---

## Comment 1

> Username: kotika  
> Created at: 2019-11-26 13:47:04 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-558635425  

> On Nov 26, 2019, at 12:12, danieltuzes <notifications@github.com> wrote:  
>   
> Sometimes I get the same random number from a uniform distribution using a Mersenne Twister engine even I properly used the engine and iterated it. I know that the number of possible states of the engine is finite and number of possible generated values is also finite, but this is not the case now.  
>   
> Using boost's implementation, 1e6 number of uniformly distributed random values are generated on the range [0; 1e7). That means that there way more possible values than required number of random values. However, I get quite often the same values, sometimes more than 100 times in this range. How is it possible?  
  
Congratulations! You have rediscovered that most of the available generators (both in boost and std::random) are not capable of full double precision.  
  
The specific generator, mt19937, natively produces 32-bit numbers, so when they are promoted to doubles via (x)/2^32 * 10^7 there are only  
a few billion distinct values. The number of collisions that your test produces, about 100,   
for a 10^6 samples seems about right based on the birthday paradox - but would be nice to check vs the exact formula.  
  
If you need a true 64-bit generator which passes all statistical tests, use my MIXMAX, it is a drop-in addition to [boost][random]   
at this point in time, at   
https://github.com/kotika/random  
  
Best Regards,  
Kostas Savvidis  
  
============================================================================================  
Institute of Nuclear and Particle Physics  
NCSR Demokritos  
  
http://inspirehep.net/author/profile/K.G.Savvidy.1  
https://github.com/kotika/random  
https://mixmax.hepforge.org

---

## Comment 2

> Username: danieltuzes  
> Created at: 2019-11-26 14:16:09 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-558648271  

How would you than describe the cases I mentioned at **It is the distribution generator**? I.e., if I use std's MT engine and pass it to std's uniform distribution, I get the proper distribution, but if I pass it to boost's uniform distribution, I get the issue? Statistics are available on SO.  
  
Could you please suggest a way (a complete example with the same features I posted above), using boost, to generate uniformly distributed **double** values on a range? Bc it seems ATM that it is not possible with boost's (random and) uniform distribution, but with std's (random and) uniform distribution. Or did you mean that it is impossible with boost ATM, and I should use copy your code code and forget about boost's implementation?

---

## Comment 3

> Username: kotika  
> Created at: 2019-11-26 14:42:22 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-558660337  

> On Nov 26, 2019, at 16:16, danieltuzes <notifications@github.com> wrote:  
> Could you please suggest a way (a complete example with the same features I posted above), using boost, to generate uniformly distributed double values on a range?  
>   
  
I dont think there can possibly be a problem with boosts distribution generation. It is a result of limited resolution of 32-bit, 31-bit, and 24-bit generators.  
Here is a faster performing program which shows that there is no problem with mt19937_64 and mixmax.   
  
Cheers,  
Kostas  
===================================================================================================================  
  
#include <iostream>  
#include <chrono>  
#include <algorithm>    // std::sort  
#include <vector>       // std::vector  
#include <array>  
  
#include <boost/random/mersenne_twister.hpp>          // random number generator  
#include <boost/random/ranlux.hpp>  
#include <boost/random/mixmax.hpp>  
  
#include <boost/random/uniform_real_distribution.hpp> // uniform distribution generator  
using namespace std;  
  
int main()  
{  
    size_t seed = static_cast<int> (std::chrono::system_clock::now().time_since_epoch().count());  
    cout << "seed = " << seed << endl;  
      
    boost::random::mixmax engine(seed);                         // the random number generator engine  
    // boost::random::mt19937_64 engine(seed);                     // the random number generator engine  
    boost::random::uniform_real_distribution<double> u(0, 1e7);  // uniformly distributed double values on the range [0; 1e7)  
    cout.precision(20);  
    const unsigned int p=1000000;  
    array<double,p> history;                                      // stores the generated values for comparison  
    for (size_t i = 0; i < p; ++i)  
    {  
        history[i] = (u(engine));  
    }  
    std::sort (history.begin(), history.end());  
          
    int k=0;  
    for (size_t j = 0; j < p-1; ++j)  
            if (history[j] == history[j+1])  
            {cout << "Equal values ("<< history[j] <<") at ID = "  << j << endl; k++; }  
      
    cout << "Found k=" << k << " equal values."<< endl;  
}

---

## Comment 4

> Username: danieltuzes  
> Created at: 2019-11-26 16:45:45 UTC  
> Updated at: 2019-11-26 17:01:45 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-558717500  

I cannot test your code because my boost doesn't come with mixmax, so `#include <boost/random/mixmax.hpp>` fails. Or do you mean that boost 1.71 is not suitable to produce random numbers as good as the implementations from MSVS and gcc, and you suggest me to use your yet-3rdparty implementation? I make the requirement more precise: I need a solution that relies only on the available boost and a complete, compact code, let's say, a header file, without additional libraries and folders of sources files.  
  
Your suggestion provides another way to generate numbers from the random number generator engine but uses the same distribution. As I pointed out, there is possibly no error with the engine but with the distribution.

---

## Comment 5

> Username: danieltuzes  
> Created at: 2019-11-26 17:24:11 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-558734202  

@kotika You are right about that it is only an effect of the too discrete values produced by the distribution, as it can be deduced [from the figure](https://imgur.com/a/OjmFff0).  
  
The figure shows that std's distribution (using boost's MT engine) follows well the expected cumulative distribution while boost's solution, the solid green line doesn't. The blue line is obtained by shifting the green line by the units q = 2.32831e-10 in _x_, supposing that an inner floor was applied somewhere, therefore the y points measured at _x_ = x0, x1, ... correspond to x = x0+q, x1+q, ...  
  
But as I measured, the same boost MT engine with std's distribution produces a much better distribution. Both with the std from MSVS and g++.

---

## Comment 6

> Username: kotika  
> Created at: 2019-11-26 18:43:47 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-558765559  

> On Nov 26, 2019, at 19:24, danieltuzes <notifications@github.com> wrote:  
>   
> @kotika <https://github.com/kotika> You are right about that it is only an effect of the too discrete values produced by the distribution, as it can be deduced from the figure <https://imgur.com/a/OjmFff0>.  
>   
> The figure shows that std's distribution (using boost's MT engine) follows well the expected cumulative distribution while boost's solution, the solid green line doesn't. The blue line is obtained by shifting the green line by the units q = 2.32831e-10 in x, supposing that an inner floor was applied somewhere, therefore the y points measured at x = x0, x1, ... correspond to x = x0+q, x1+q, ...  
>   
  
I have not looked at GCC or MSVC implementations, but my first guess is that they are using generate_canonical   
which is capable to produce good quality distributions from inferior generators. (at a huge performance cost)  
  
My suggestion is to use a better generator, either MT64 or my MIXMAX.  
  
MIXMAX for Boost is at https://github.com/kotika/random <https://github.com/kotika/random>  
  
MIXMAX is also available for GSL, for ROOT, and inside specialized simulation packages Geant4 and PYTHIA.  
  
Best Regards,  
Kostas

---

## Comment 7

> Username: jzmaddock  
> Created at: 2019-11-26 19:52:57 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-558791725  

It's boost's distribution, but I don't see off hand where the issue is.  
  
I tried the following code:  
  
```  
   boost::random::mt19937 dist;  
   boost::random::uniform_real_distribution<double> d(0, 100);  
  
   std::map<double, unsigned> m;  
  
   for (unsigned i = 0; ; ++i)  
   {  
      double dd = d(dist);  
      if (m[dd]++)  
      {  
         std::cout << "Collision on value " << dd << " after " << i << " iterations with collision count " << m[dd] << std::endl;  
      }  
   }  
```  
  
On MSVC, and there are thousands of collisions, but strangely the collision count on any given value never rises above 2.  
  
If I replace `boost::random::uniform_real_distribution` with `std::uniform_real_distribution` but keep the generator the same, then there are NO collisions.  
  
I have scanned through the 2 codebases - `uniform_real_distribution` isn't actually that complex - but I can't spot the difference between the msvc and boost code at present.

---

## Comment 8

> Username: danieltuzes  
> Created at: 2019-11-26 20:56:47 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-558813795  

@peteroupc Have you tried your suggestion? Was it working?

---

## Comment 9

> Username: danieltuzes  
> Created at: 2019-11-26 21:09:30 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-558818069  

@kotika Once again, the MT engine is just fine. Boost's MT engine is good enough bc std's distribution produces good results with it. Std's MT engine is also good for the same reason.  
  
Boost's distribution is bad, bc on both good engine it produces bad results, bc the produced small numbers are not evenly distributed even for a 32bit float. You can still use a 64bit engine that hides this problem, but one should make it clear than that uniform_real_distribution doesn't produce even float-uniformly distributed values from a 32bit engine.

---

## Comment 10

> Username: peteroupc  
> Created at: 2019-11-26 21:09:36 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-558818114  

> @peteroupc Have you tried your suggestion? Was it working?  
  
I took it back, sorry.

---

## Comment 11

> Username: kotika  
> Created at: 2019-11-27 12:47:05 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-559073392  

> On Nov 26, 2019, at 23:09, danieltuzes <notifications@github.com> wrote:  
> one should make it clear than that uniform_real_distribution doesn't produce even float-uniformly distributed values from a 32bit engine.  
>   
  
There is no way to produce one random floating point value with double precision (53bits) from one random 32bit value.   
The way GCC and MSVC chosen to do it is use two 32bit values and paste them together with generate_canonical.  
This is the reason you were seeing those small differences between std and boost,   
and the every-other-number-is-close phenomenon:   
  
(std)                                                   (boost)                                                           (boost)  
3354420.9766563926823  == 3354420.976247638464 + 1/2^32 * 1755586.0406719148159   
8773544.1042856499553  == 8773544.1024415194988 + 1/2^32 * 7920481.4555123448372  
So the problem is "solved" but at the cost of drawing from the generator twice to produce one double.  
  
The true and permanent solution  
is to deprecate old generators and use better high-resolution generators, which like I said, means at the moment: MT64 and MIXMAX.  
  
Cheers,  
Kostas  
  
> On Nov 26, 2019, at 12:12, danieltuzes <notifications@github.com> wrote:  
>   
> Note  
>   
> A strange behavior can be seen if one compares the generated values with the one std::random generates. Example for values from random::boost for seed 4561565448989 is  
>   
> 1755586.0406719148159  
> 3354420.976247638464   <--  
> 3630764.0071026980877  
> 3488445.2889673411846  <--  
> 7920481.4555123448372  
> 8773544.1024415194988  <--  
> while standard library generates  
>   
> 3354420.9766563926823  <--  
> 3488445.2898126943037  <--  
> 8773544.1042856499553  <--

---

## Comment 12

> Username: danieltuzes  
> Created at: 2019-11-27 14:48:53 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-559118162  

Could you please give this as an answer on my [SO question](https://stackoverflow.com/questions/59039358/boostrandom-generates-identical-values-too-often-from-the-same-seed-at-differe) so I can accept it? Or do you mind if I copy it instead of you?

---

## Comment 13

> Username: peteroupc  
> Created at: 2019-11-27 15:06:38 UTC  
> Updated at: 2019-11-27 17:06:52 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-559125490  

I want to point out that [`boost::random::uniform_real_distribution`](https://github.com/boostorg/random/blob/master/include/boost/random/uniform_real_distribution.hpp#L33), at line 33, generates one real number (of type `T`) from only one integer (of type `Engine::result_type`) from the random engine, and that the method doesn't care what the size and precision of `T` and `Engine::result_type` are.  Notably, the fewer bits `Engine::result_type` has (in comparison to the precision of `T`, which for `double` is usually 53 bits), the fewer values will be spread across the range given to the generator.  However, if that method took enough values from the random engine to be close to the precision of `T` (rather than just one value), this problem is reduced.  
  
For example, if `T` is `double` the method could detect that the size of `Engine::result_type` is less than 53 bits, and if so, take two or more values from the random engine (rather than one) to generate each real number.  This lessens the risk of using random engines with 32-bit outputs, but there are two disadvantages:  
  
- This method is [not exactly trivial to implement](https://peteroupc.github.io/randomfunc.html#Uniform_Random_Real_Numbers), given that the distribution allows an arbitrary range.  
- The method will not be backward compatible with previous versions.  
  
This is a suggestion that I have not tried, but I believe it may help reduce the risk of using random engines with 32-bit or smaller outputs.  
  
> The true and permanent solution  
is to deprecate old generators and use better high-resolution generators, which like I said, means at the moment: MT64 and MIXMAX.  
  
The quality of a PRNG does not depend on the size of random numbers it outputs (e.g., 32- or 64-bit numbers), but rather it depends on its state size, its period, and whether the numbers behave like independent uniform random numbers.

---

## Comment 14

> Username: kotika  
> Created at: 2019-11-27 17:46:49 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-559189229  

> On Nov 27, 2019, at 17:06, Peter Occil <notifications@github.com> wrote:  
>   
> This is a suggestion that I have not tried, but I believe it may help reduce the risk of using random engines with 32-bit outputs.  
  
This "suggestion" is what gcc and MSVC implement, as I pointed out.   
It costs more in performance than using a better generator.  
Using a better generator is free, so do it. For example, my generator, MIXMAX  
gives 61 bit integers, which is enough to produce 53bit  
double precision numbers, is as fast to give you those 61 bits as  MT gives a 32bit int,   
is smaller in memory than MT and passes all statistical tests unlike MT.  
  
(MT64 is also good enough, but at least on my machine, it is 3 times slower)  
  
Cheers,  
Kostas

---

## Comment 15

> Username: mathemaphysics  
> Created at: 2020-12-30 03:58:59 UTC  
> Url: https://github.com/boostorg/random/issues/61#issuecomment-752319513  

Maybe this has been discovered already, but I got the same result using boost's `uniform_real_distribution` with the Mersenne generators definitively because I was accidently re-seeding the generator/engine using `srand((unsigned)time(0))` followed by handing `rand()` to any of the engines as seed.  
  
Once I made sure `srand((unsigned)time(0))` was only called once before the boost RNG was initialized with `rand()` as seed and that I wasn't creating multiple boost RNGs to produce my ensemble averages it worked out nicely. No more odd repeated numbers showing up in the distribution.
