# #81 - Unexpected behavior binomial distribution with np ~ 1 n very large and p very small [Closed]

> Username: vchz  
> Created at: 2021-02-17 15:04:58 UTC  
> Updated at: 2024-06-21 23:22:07 UTC  
> Closed at: 2024-06-21 23:22:07 UTC  
> Url: https://github.com/boostorg/random/issues/81  

Hello,  
  
While performing Monte-Carlo simulations, I ran into a very strange behavior with binomial implementation of boost 1.75.0, along with std. It also happens with some earlier version of boost that I tried. Here is a sample code  
  
`  
#include <random>  
#include <boost/random.hpp>  
#include <iostream>  
  
int main ( int argc, char* argv[] ) {  
  
    std::random_device rd;  
    std::mt19937 rnd_1(rd());  
    std::mt19937 rnd_2(rnd_1);  
  
    std::binomial_distribution<long long> Std_bin(64279706454719456, 6.27043e-17);  
  
    double a = 0;  
    double b = 0;  
    int n = 100;  
  
    for (int i = 0; i < n; i++) {  
    	a += Std_bin(rnd_1);  
    }  
    std::cout << "Std mean " << a/n << std::endl;  
  
    boost::random::binomial_distribution<long long> Boost_bin(64279706454719456, 6.27043e-17);  
  
    for (int i = 0; i < n; i++) {  
    	b += Boost_bin(rnd_2);  
    }  
    std::cout << "Boost mean " << b/n << std::endl;  
  
    return 0;  
}  
`  
  
I compile with g++ (Debian 6.3.0-18+deb9u1) 6.3.0 20170516 with option -std=C++1. What is expected is a value close to 4, which is approximately the average of the binomial for these parameters.  
  
The behavior of both the boost and the std algorithm are wrong here, with std giving a wrong estimation (around 7 or 8) and boost providing an estimation around 29. More than this, the variate returned by the Boost binomial is almost always 29, with rarely a different value. This behavior doesn't seem depend on the seed or choice of random generator.  
  
I am not sure how exactly the different algorithms have been written or if this is a problem on my side, but I know for a fact that this is possible to generate fastly and correctly from a binomial variate with the same parameters as here, since numpy can do it.

---

## Comment 1

> Username: kotika  
> Created at: 2021-02-18 14:23:04 UTC  
> Updated at: 2021-02-18 20:22:14 UTC  
> Url: https://github.com/boostorg/random/issues/81#issuecomment-781378262  

The input seems highly unreasonable to me. You are taking a p which is two orders of magnitude smaller than machine double precision epsilon.  
  
Developers can be expected to stress their implementations of numeric algorithms, and support users who are having difficulties in the real world.  
It would be nice of you to provide an analysis of what went wrong and propose solutions.  
You can start by reading the source code of binomial_distribution.hpp  
  
Cheers,  
Kostas

---

## Comment 2

> Username: vchz  
> Created at: 2021-02-18 15:13:59 UTC  
> Url: https://github.com/boostorg/random/issues/81#issuecomment-781413248  

Hello Kostas,  
  
I agree these values are "unreasonable", especially for n, and I have scaled down my use not to reach such extreme values. I am not gonna argue about what real world applications are. I am not sure I get your point about double precision, 1e-17 is very well defined, and any other scientific language can cope with this kind of mildly extreme input.  
  
Let me stress what I may not have said in my first message, I am not here to complain about developers, especially because developers are not asked to do computer science or algorithmics. I still think that this may be an issue worth noticing, hence my post. If I have some time I'll try to see how numpy implementation of the binomial_distribution is better than the std's and boost's one.

---

## Comment 3

> Username: kotika  
> Created at: 2021-02-18 18:15:23 UTC  
> Url: https://github.com/boostorg/random/issues/81#issuecomment-781539526  

> On Feb 18, 2021, at 17:14, Victor <notifications@github.com> wrote:  
>   
>  1e-17 is very well defined, and any other scientific language can cope with this kind of mildly extreme input  
  
double precision numbers have 52-53 bits of precision, 2^-52 is 10^-15.   
>  I still think that this may be an issue worth noticing, hence my post. If I have some time I'll try to see how numpy implementation of the binomial_distribution is better than the std's and boost's one.  
>   
Most likely, numpy is working with arbitrary precision numbers. std is doing slightly better on your example than boost but still wrong.  
  
K

---

## Comment 4

> Username: kotika  
> Created at: 2021-02-18 18:32:34 UTC  
> Updated at: 2021-02-18 20:23:54 UTC  
> Url: https://github.com/boostorg/random/issues/81#issuecomment-781550310  

> On Feb 18, 2021, at 17:14, Victor <notifications@github.com> wrote:  
>   
>  I still think that this may be an issue worth noticing, hence my post. If I have some time I'll try to see how numpy implementation of the binomial_distribution is better than the std's and boost's one.  
  
For values you are interested, the binomial distribution mathematically tends to the poisson  
with lambda = 64279706454719456*6.27043e-17 = 4.03. That of course works fine in both std and boost.

---

## Comment 5

> Username: vchz  
> Created at: 2021-02-19 17:32:35 UTC  
> Url: https://github.com/boostorg/random/issues/81#issuecomment-782223425  

Definitely that's a workaround, but that's still very intriguing that the algorithm breakdown, the problem very likely is at the level of the algorithm used to generate this variate using the inverse of binomial distribution. Let's just cross fingers that it doesn't happen for slightly smaller values
