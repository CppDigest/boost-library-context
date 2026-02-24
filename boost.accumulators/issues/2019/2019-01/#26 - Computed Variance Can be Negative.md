# #26 - Computed Variance Can be Negative [Open]

> Username: NAThompson  
> Created at: 2019-01-19 20:03:19 UTC  
> Updated at: 2024-04-17 13:24:08 UTC  
> Url: https://github.com/boostorg/accumulators/issues/26  

This is an example from [Golub's paper](https://apps.dtic.mil/dtic/tr/fulltext/u2/a133112.pdf):  
  
```cpp  
#include <iostream>  
#include <vector>  
#include <random>  
#include <cmath>  
#include <boost/accumulators/accumulators.hpp>  
#include <boost/accumulators/statistics.hpp>  
  
template<class Real>  
void golub_example()  
{  
    std::random_device rd{};  
    auto seed = rd();  
    std::mt19937 gen(seed);  
    std::normal_distribution<Real> dis(500.0, 0.01);  
  
    std::vector<Real> v(50000);  
    for(size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = dis(gen);  
    }  
  
    accumulator_set<Real, stats<tag::variance(lazy)>> acc;  
    acc = std::for_each(v.begin(), v.end(), acc);  
    std::cout << "Variance = " << variance(acc) << "\n";  
    std::cout << "Seed = " << seed << "\n";  
}  
  
int main()  
{  
   golub_example<float>();  
}  
```  
  
Output:  
  
```bash  
Variance (accumulator) = -79.9219  
Seed = 1925235287  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-06-29 12:15:09 UTC  
> Updated at: 2019-06-29 12:40:05 UTC  
> Url: https://github.com/boostorg/accumulators/issues/26#issuecomment-506952705  

@CromwellEnage : I saw on the mailing list that you are now looking to maintain this library; could you fix this bug? The fix is in Higham's book Accuracy and Stability of Numerical Algorithms, first chapter.

---

## Comment 2

> Username: CromwellEnage  
> Created at: 2019-06-29 13:39:26 UTC  
> Url: https://github.com/boostorg/accumulators/issues/26#issuecomment-506957729  

@NAThompson, I'll look into it and let you know when I or someone else have implemented a solution.

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-06-29 14:19:14 UTC  
> Url: https://github.com/boostorg/accumulators/issues/26#issuecomment-506960304  

@CromwellEnage : [This](https://github.com/boostorg/math/blob/develop/include/boost/math/tools/univariate_statistics.hpp) also implements the correct algorithm.

---

## Comment 4

> Username: antoine79  
> Created at: 2024-04-17 13:23:00 UTC  
> Updated at: 2024-04-17 13:24:08 UTC  
> Url: https://github.com/boostorg/accumulators/issues/26#issuecomment-2061252778  

@NAThompson , **variance(lazy)** by definition is lazy and computes variance as the difference between the two accumulators. This issue can be closed in my opinion as **variance(immediate)** exists already and behaves very closely to the algorithm that you point out.   
See:  
```cpp  
#include <iostream>  
#include <vector>  
#include <random>  
#include <cmath>  
#include <boost/accumulators/accumulators.hpp>  
#include <boost/accumulators/statistics.hpp>  
  
using namespace boost::accumulators;  
  
template<class Real>  
void golub_example()  
{  
    std::mt19937 gen(1925235287);  
    int N = 50000;  
    std::normal_distribution<Real> dis(500.0, 0.01);  
  
    std::vector<Real> v(N);  
    for(size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = dis(gen);  
    }  
  
    accumulator_set<Real, stats<tag::variance(immediate)>> acc;  
    acc = std::for_each(v.begin(), v.end(), acc);  
    std::cout << "Variance = " << variance(acc) << "\n";  
}  
  
int main()  
{  
   golub_example<float>();  
}  
```  
Output:  
```  
Variance = 0.000100213  
```  
  
Now, if N=500e3 instead of 50e3, then we have another numeric problem as the variance becomes 0.7 instead of 1e-4. Another algorithm becomes necessary, like the shifted algorithm where variance is computed by subtracting from each value the value of the first data point. I have a proposal if that's of interest to anyone.
