# #104 - MixMax RNG with variate_generator returns constant values when initialised with seed = 0 [Open]

> Username: andrjohns  
> Created at: 2024-05-17 11:53:52 UTC  
> Updated at: 2025-01-30 16:04:44 UTC  
> Url: https://github.com/boostorg/random/issues/104  

When the `mixmax` RNG is initialised with a seed of 0 and used with `variate_generator`, repeated calls always produce the same value:  
  
Reproducible example ([here on godbolt](https://godbolt.org/z/jPa87dadY)):  
  
```cpp  
#include <boost/random.hpp>  
#include <iostream>  
  
int main() {  
  using boost::normal_distribution;  
  using boost::random::uniform_real_distribution;  
  using boost::variate_generator;  
  using boost::random::mixmax;  
  
  mixmax rng(0);  
  variate_generator<mixmax&, normal_distribution<> > norm_rng(rng, normal_distribution<>(5, 10));  
  variate_generator<mixmax&, uniform_real_distribution<> > unif_rng(rng, uniform_real_distribution<>(5.0, 10.0));  
  
  for (size_t i = 0; i < 5; ++i) {  
    std::cout   << "Normal(5,10): " << norm_rng() << "\n"  
                << "Uniform(5,10): " << unif_rng() << "\n";  
  }  
    std::cout << std::endl;  
    return 0;  
}  
```  
  
Returns:  
  
```  
Normal(5,10): 5  
Uniform(5,10): 5  
Normal(5,10): 5  
Uniform(5,10): 5  
Normal(5,10): 5  
Uniform(5,10): 5  
Normal(5,10): 5  
Uniform(5,10): 5  
Normal(5,10): 5  
Uniform(5,10): 5  
```

---

## Comment 1

> Username: mborland  
> Created at: 2024-05-17 12:16:39 UTC  
> Url: https://github.com/boostorg/random/issues/104#issuecomment-2117470168  

@kotika Does a check need to be added to ensure the user gives a non-zero seed? We could throw `std::range_error` saying the value has to be [1, UINT32/64_MAX]

---

## Comment 2

> Username: WardBrian  
> Created at: 2024-05-17 13:46:28 UTC  
> Url: https://github.com/boostorg/random/issues/104#issuecomment-2117647330  

This seems to be the case only when the 4-argument version is called with all zeros. If the line  
  
https://github.com/boostorg/random/blob/0190232db76002f2236ac1f3df1ef17c4ffa1393/include/boost/random/mixmax.hpp#L183-L184  
  
was updated to e.g. ` seed_uniquestream( &S, 0,  1,  (uint32_t)(seedval>>32), (uint32_t)seedval ); `   
  
Then the issue appears to go away  
  
https://arxiv.org/pdf/1403.5355 includes the line   
>For the purposes of generating pseudo-random numbers with this method, one chooses the initial vector u(0), called the “seed”, with at **least one non-zero component**  
  
(emphasis mine), which seems to imply any restriction should just be on the full four-argument form. The uint64_t overload could be made to never have an invalid value if at least one of the two other arguments were nonzero, I believe.   
  
My question is if the statement "`random numbers are statistically independent from any two distinct seeds, e.g. consecutive seeds are ok`" remains true in this case.  
  
Several other implementations do seem to trap the seed being 0:   
https://github.com/ramos/mxmx/blob/master/src/mixmax.f90#L180-L181  
https://root.cern/doc/master/mixmax_8icc_source.html#l00212

---

## Comment 3

> Username: kotika  
> Created at: 2025-01-30 16:03:59 UTC  
> Updated at: 2025-01-30 16:04:44 UTC  
> Url: https://github.com/boostorg/random/issues/104#issuecomment-2624910645  

The seeding method has been significantly improved since the time ROOT and FORTRAN versions were produced.   
In the new version which boost ships, the all-zero seed  results a non-zero initial vector as required. Specifically it results in the unit initial vector:  
{1,0,0,...0}. The random numbers produced from this initial vector are just as good as from any other seed, but it  needs to warm up - just in that special case. Spit out approx 72 values, and after that it is just fine.
