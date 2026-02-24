# #32 Add .cbegin() and .cend(). [Merged]

> Username: NAThompson  
> Created at: 2019-12-12 16:21:48 UTC  
> Updated at: 2019-12-14 00:06:35 UTC  
> Merged at: 2019-12-14 00:06:34 UTC  
> Closed at: 2019-12-14 00:06:34 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/32  

The following example shows how now the code compiles with `boost::math::statistics`, but previously did not:  
  
```cpp  
#include <iostream>  
#include <boost/circular_buffer.hpp>  
#include <boost/math/statistics/univariate_statistics.hpp>  
  
template<class RandomAccessContainer>  
void print_container(RandomAccessContainer const & v) {  
    if (v.size() == 0) {  
        std::cout << "{}\n";  
        return;  
    }  
    std::cout << "{";  
    for (size_t i = 0; i < v.size() - 1; ++i) {  
        std::cout << v[i] << ", ";  
    }  
    std::cout << v[v.size()-1] << "}\n";  
    return;  
}  
  
int main() {  
    boost::circular_buffer<double> v(12);  
    std::cout << "v.size() = " << v.size() << "\n";  
    std::cout << "v.capacity() = " << v.capacity() << "\n";  
    for (size_t i = 0; i < v.capacity(); ++i) {  
        v.push_back(i);  
    }  
  
    print_container(v);  
    std::cout << "Mean(v) = " << boost::math::statistics::mean(v) << "\n";  
    std::cout << "Variance(v) = " << boost::math::statistics::variance(v) << "\n";  
    v.push_back(12);  
    print_container(v);  
    std::cout << "Mean(v) = " << boost::math::statistics::mean(v) << "\n";  
    std::cout << "Variance(v) = " << boost::math::statistics::variance(v) << "\n";  
  
    v.push_back(13);  
    print_container(v);  
    std::cout << "Mean(v) = " << boost::math::statistics::mean(v) << "\n";  
    std::cout << "Variance(v) = " << boost::math::statistics::variance(v) << "\n";  
  
}  
```

---

## Review 1 [Commented]

> Username: glenfe  
> Created_at: 2019-12-12 16:26:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/circular_buffer/pull/32#pullrequestreview-331357255  

Can't `cbegin()` just `return begin();` and  `cend()` just `return end();` ?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2019-12-12 16:30:14 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/32#issuecomment-565081355  

@glenfe : Yup, you're right.  
  
Also might make sense to reverse the code, so `cbegin()` contains the definition, and `begin()` call `cbegin()` in a `const` context. But perhaps that's bikeshedding.

---

## Review 3 [Commented]

> Username: glenfe  
> Created_at: 2019-12-12 16:31:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/circular_buffer/pull/32#pullrequestreview-331361256  

Looks good. New public member functions deserve at least one test case that exercises them.

---

## Comment 4

> Username: glenfe  
> Created_at: 2019-12-12 16:37:35 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/32#issuecomment-565084387  

Thanks. I'll merge after Travis completes.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2019-12-13 21:19:38 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/32#issuecomment-565612308  

@glenfe : Looks like it finished successfully.

---
