# #309 - Performance regression of boost::container::static_vector introduced in boost v1.86 [Closed]

> Username: mateuszmar2  
> Created at: 2025-08-14 10:30:48 UTC  
> Updated at: 2025-08-14 18:15:19 UTC  
> Closed at: 2025-08-14 18:14:37 UTC  
> Url: https://github.com/boostorg/container/issues/309  

Hi, we noticed performance regression of our application caused by boost 1.81 -> 1.86 upgrade.  
We often use `boost::container::static_vector`. We iterate a lot on elements allocated in the `static_vector` but we do not essentially allocate/de-allocate those frequently.  
With this knowledge we created a minimal problem demonstrator which in our understanding reproduces the same problem:  
```  
#include <boost/container/static_vector.hpp>  
#include <chrono>  
#include <iostream>  
  
int main() {  
    constexpr size_t num_elements = 1000;  
    constexpr size_t num_iterations = 1000000;  
  
    boost::container::static_vector<int, num_elements> vec;  
    for (size_t i = 0; i < num_elements; ++i) {  
        vec.push_back(i);  
    }  
  
    auto start = std::chrono::high_resolution_clock::now();  
    long long sum = 0;  
    for (size_t iteration = 0; iteration < num_iterations; ++iteration) {  
        for (const auto& elem : vec) {  
            sum += elem;  
        }  
    }  
    auto end = std::chrono::high_resolution_clock::now();  
  
    std::chrono::duration<double> duration = end - start;  
    std::cout << "Time taken: " << duration.count() << " seconds\n";  
    std::cout << "Sum: " << sum << "\n";  
  
    return 0;  
}  
```  
  
We analyzed changes done between v1.81 and v1.86 and this commit seems to be the root cause:  
https://github.com/boostorg/container/commit/978bbb113ab2e72f84f626d3e52039cf06501853  
We reduced this change further and found out that exactly following part is problematic:  
https://github.com/boostorg/container/commit/978bbb113ab2e72f84f626d3e52039cf06501853#diff-7c03ab8f8917f0a8eae85d74120a7d943500501cbfc8a3a0fa61be9d5bddc475R67  
  
We were able to restore previous performance with following partial revert in `include/boost/container/static_vector.hpp`:  
```  
@@ -64,7 +64,7 @@  
    {  return *this;  }  
   
    inline T* internal_storage() const BOOST_NOEXCEPT_OR_NOTHROW  
-   {  return move_detail::launder_cast<T*>(&storage);  }  
+   {  return const_cast<T*>(static_cast<const T*>(static_cast<const void*>(storage.data)));  }  
   
    BOOST_STATIC_CONSTEXPR std::size_t internal_capacity = N;  
   
```  
  
Test results:  
- boost v1.81: Time taken: 0.222141 seconds  
- boost v1.86: Time taken: 0.472829 seconds  
- boost v1.86 with mentioned patch in `static_vector.hpp` applied: Time taken: 0.222295 seconds  
  
It also reproducible on boost v1.88. We compile our application with `-O3` for x86_64, but we noticed that `-fvect-cost-model=` is crucial. It has to be `cheap` or `dynamic` (enabled in `-O3`). With `very-cheap` (enabled in `-O2`) both v1.86 tests will have the same (worse) result. It seems that usage of `launder_cast` prevents gcc from doing vectorization here. Our application has experienced this problem with gcc12.2.0 and gcc14.2.0. Presented demonstrator reproduces this issue only with gcc14.1.0 or newer.  
  
Theoretically it can be checked on godbolt, but we shouldn't fully trust it when it comes to performance:  
https://godbolt.org/z/dGbod9Kz9  
  
Thanks in advance for your support.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-08-14 18:15:19 UTC  
> Url: https://github.com/boostorg/container/issues/309#issuecomment-3189437170  

Many thanks for you analysis and patch.
