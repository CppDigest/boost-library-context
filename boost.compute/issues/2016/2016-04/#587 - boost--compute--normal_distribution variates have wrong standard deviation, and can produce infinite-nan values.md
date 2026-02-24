# #587 - boost::compute::normal_distribution variates have wrong standard deviation, and can produce infinite/nan values [Closed]

> Username: jagerman  
> Created at: 2016-04-21 20:04:37 UTC  
> Updated at: 2016-12-05 01:46:51 UTC  
> Closed at: 2016-04-26 11:42:28 UTC  
> Url: https://github.com/boostorg/compute/issues/587  

The normal_distribution implementation in boost::compute has some problems.  
  
First, it generates values with standard deviation about 1.2 times what was requested.  The following code demonstrates:  
  
```  
#include <boost/compute/system.hpp>  
#include <boost/compute/command_queue.hpp>  
#include <boost/compute/algorithm/accumulate.hpp>  
#include <boost/compute/algorithm/inner_product.hpp>  
#include <boost/compute/container/vector.hpp>  
#include <boost/compute/random/default_random_engine.hpp>  
#include <boost/compute/random/normal_distribution.hpp>  
#include <boost/compute/lambda.hpp>  
  
int main() {  
    boost::compute::device device(boost::compute::system::default_device());  
    boost::compute::context context(boost::compute::system::default_context());  
    boost::compute::command_queue queue(boost::compute::system::default_queue());  
    boost::compute::vector<float> vec(1000000, context);  
    boost::compute::default_random_engine engine(queue);  
    boost::compute::normal_distribution<float> distribution(5.0f, 1.0f);  
    distribution.generate(vec.begin(), vec.end(), engine, queue);  
    float mean = boost::compute::accumulate(vec.begin(), vec.end(), 0.f) / vec.size();  
    float mean_sq = boost::compute::inner_product(vec.begin(), vec.end(), vec.begin(), 0.f) / vec.size();  
    float stdev = sqrt(mean_sq - mean*mean);  
    std::cout << "mean: " << mean << " (expect 5); stdev: " << stdev << " (expect 1)\n";  
}  
```  
  
which outputs:  
  
mean: 5.00037 (expect 5); stdev: 1.20036 (expect 1)  
  
This is caused by these lines in boost/compute/random/normal_distribution.hpp:  
  
```  
            const RealType z1 = sqrt(-2.f * log2(x1)) * cos(2.f * M_PI_F * x2);  
            const RealType z2 = sqrt(-2.f * log2(x1)) * sin(2.f * M_PI_F * x2);  
```  
  
both of those log2 calls have to be log (i.e. using the natural logarithm).  The ~1.2 scale in the standard deviation comes out of this mathematically: log2(x) = ln(x) / ln(2), and the square root of 1/ln(2) equals 1.2011224.  i.e. the standardized variates are too large by a factor of sqrt(1/ln(2)).  
  
I _think_ (but have not verified) that there is another problem here: the generated x1 and x2 values can take on values of either 0 or a value larger than 1:  
  
```  
            const RealType x1 = x.x / (RealType) (UINT_MAX - 1);  
            const RealType x2 = x.y / (RealType) (UINT_MAX - 1);  
```  
  
When x.x equals 0, x1 = 0, which results in both z1 and z2 being infinite, which is bad.  
  
When x.x equals UINT_MAX, you can get a value larger than 1 for x1, which gives a positive value for the log, which then results in taking the sqrt of a negative, which is nan.  Note, however, that this will only shows up when RealType has sufficient precision: when RealType is float, UINT_MAX-1 (=4294967294) actually rounds to UINT_MAX+1 (=4294967296).  If RealType is double, however, you can run into the problem.  
  
Basically, x1 needs to be in (0,1], and it currently violates the strictness of the left of that range, and can, depending on RealType, violate the right end of the range.  
  
See boost/random/normal_distribution.hpp from a pre-1.56.0 version of boost (https://github.com/boostorg/random/blob/8c30753bf49504b5f677b8b410a3787907c86bd0/include/boost/random/normal_distribution.hpp) for a properly-working Box-Muller implementation (pre-1.56 because, in 1.56.0, the Box-Muller implementation was replaced with a ziggurat implementation).  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2016-04-22 21:01:05 UTC  
> Url: https://github.com/boostorg/compute/issues/587#issuecomment-213587248  

Thanks for the detailed bug report! I've pushed some fixes in PR #590, would you mind taking a look?

---

## Comment 2

> Username: pavanky  
> Created at: 2016-04-22 21:12:35 UTC  
> Url: https://github.com/boostorg/compute/issues/587#issuecomment-213590421  

@kylelutz The normalization problem still exists when using double precision. Shouldn't you be normalizing with `UINT_MAX` instead of `UINT_MAX-1` ?

---

## Comment 3

> Username: jagerman  
> Created at: 2016-04-22 21:45:45 UTC  
> Url: https://github.com/boostorg/compute/issues/587#issuecomment-213598868  

@pavanky When using double precision, it should actually be dividing by (RealType(UINT_MAX) + RealType(1)), because we want [0, 1), and UINT_MAX is a valid value for x.x.  (This is what boost::random::uniform_01 does when converting from a integer generator, as is the case here).  
  
While that would avoid the problem for RealType=double, we still have a problem for RealType=float, and it's a trickier problem to solve: for any x.x value from 4294967168 to 4294967295 (on average, 1 time in 33,554,432 draws), we're going to get exactly 1, because the implicit conversion to float in the numerator will round to 2^32:  
  
float(4294967167) = 4294967040  
float(4294967168) = 4294967296  
float(4294967169) = 4294967296  
...  
float(4294967295) = 4294967296  
  
and that means we're going to get a 1 that we don't want.  
  
b::r::uniform_01 gets around this problem by using a loop that almost always runs once--it would only repeat, for float from a uint32_t engine, for one of these 128 values.  Obviously that isn't an option here, but I'm not entirely sure how to get around the issue.

---

## Comment 4

> Username: jagerman  
> Created at: 2016-04-22 21:55:48 UTC  
> Updated at: 2016-04-22 21:58:07 UTC  
> Url: https://github.com/boostorg/compute/issues/587#issuecomment-213602032  

And it seems boost/compute/random/uniform_real_distribution.hpp has the same problem:  
  
`return LO + (convert_RealType(x) / MAX_RANDOM) * (HI - LO);`  
  
but when RealType(x) == MAX_RANDOM, that division equals 1, and hence it returns HI, even though it's supposed to return a value in [LO, HI).  
  
boost::random::uniform_real_distribution uses a loop that only returns when the value is strictly less than the upper limit (exactly the same way boost::random::uniform_01 works).

---

## Comment 5

> Username: jagerman  
> Created at: 2016-04-22 22:49:06 UTC  
> Url: https://github.com/boostorg/compute/issues/587#issuecomment-213616023  

Okay, I think I worked out a solution.  Will submit a PR shortly.

---

## Comment 6

> Username: jagerman  
> Created at: 2016-04-22 23:37:29 UTC  
> Url: https://github.com/boostorg/compute/issues/587#issuecomment-213626913  

Okay, see PR #591 (I included the commits from PR #590 in it).  
  
Here's my (non-compute, c++11) test code that shows this working:  
  
```  
#include <iostream>  
#include <cmath>  
#include <climits>  
  
template <typename RealType>  
void test() {  
    const RealType one = 1;  
    for (unsigned int i = 4294967000; i > 1000 or i < 300; i++) {  
        RealType x = std::nextafter(RealType(i) / RealType(UINT_MAX), RealType(0));  
        std::cout << i << ": 1-x = " << one-x << std::endl;  
    }  
}  
  
int main() {  
    std::cout.precision(20);  
    std::cout << "float:\n"; test<float>();  
    std::cout << "double:\n"; test<double>();  
}  
```  
  
What I get with that, for float, is:  
  
```  
...  
4294967293: 1-x = 5.9604644775390625e-08  
4294967294: 1-x = 5.9604644775390625e-08  
4294967295: 1-x = 5.9604644775390625e-08  
0: 1-x = 1  
1: 1-x = 1  
2: 1-x = 1  
3: 1-x = 1  
...  
128: 1-x = 1  
129: 1-x = 0.99999994039535522461  
...  
```  
  
and for double, everything gets a unique value, with the end-points producing:  
  
```  
4294967294: 1-x = 2.3283075467617209142e-10  
4294967295: 1-x = 1.1102230246251565404e-16  
0: 1-x = 1  
1: 1-x = 0.99999999976716935635  
```  
  
So this gives the desired [0,1), and 1-x is in the desired (0,1] range.

---

## Comment 7

> Username: jagerman  
> Created at: 2016-04-22 23:43:44 UTC  
> Url: https://github.com/boostorg/compute/issues/587#issuecomment-213627950  

Also, I didn't commit or test this, but I think the same sort of thing would work for uniform_real_distribution, i.e. instead of:  
  
`return LO + (convert_RealType(x) / MAX_RANDOM) * (HI - LO);`  
  
use:  
  
`return nextafter(LO + (convert_RealType(x) / MAX_RANDOM) * (HI - LO), (RealType) LO);`

---

## Comment 8

> Username: jszuppe  
> Created at: 2016-04-24 14:11:42 UTC  
> Url: https://github.com/boostorg/compute/issues/587#issuecomment-213964305  

@jagerman so `uniform_real_distribution` still need a fix, yes?

---

## Comment 9

> Username: jagerman  
> Created at: 2016-04-24 14:57:38 UTC  
> Url: https://github.com/boostorg/compute/issues/587#issuecomment-213978644  

@haahh Yes, my PR did not address that.

---

## Comment 10

> Username: jszuppe  
> Created at: 2016-04-26 07:16:39 UTC  
> Url: https://github.com/boostorg/compute/issues/587#issuecomment-214647981  

I think this one can be closed. Fixed by https://github.com/boostorg/compute/pull/591 and https://github.com/boostorg/compute/pull/595.
