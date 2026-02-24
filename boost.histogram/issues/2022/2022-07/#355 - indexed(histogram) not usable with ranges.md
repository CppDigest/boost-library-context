# #355 - indexed(histogram) not usable with ranges [Open]

> Username: mreininghaus  
> Created at: 2022-07-15 15:54:05 UTC  
> Updated at: 2022-07-20 08:55:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/355  

While a `boost::histogram` can be used as a range in C++20, an `indexed(histogram)` cannot. At least for me (tested with clang++ 13 and g++ 11.3), the following code does not compile:  
  
```c++  
#include <random>  
#include <ranges>  
#include <iostream>  
  
#include <boost/histogram.hpp>  
  
int main() {  
  using namespace boost::histogram;  
  
  std::mt19937 rng;  
  std::exponential_distribution dist;  
  
  auto hist = make_histogram(axis::regular<>(20, 1, 10.0));  
  
  for (int i = 0; i < 1'000; ++i) {  
    hist(dist(rng));  
  }  
    
  // these work  
  auto r1 = std::ranges::subrange(hist.cbegin(), hist.cend());  
  auto v1 = hist | std::views::transform([] (auto&& x) {return 1;});  
    
  // these don't:  
  auto x = indexed(hist);  
  auto r2 = std::ranges::subrange(x.cbegin(), x.cend());  
  auto v2 = indexed(hist) | std::views::transform([] (auto&& x) {return 1;});  
  
  return 0;  
}  
```

---

## Comment 1

> Username: HDembinski  
> Created at: 2022-07-20 08:22:16 UTC  
> Updated at: 2022-07-20 08:22:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/355#issuecomment-1189976192  

It seems that the iterators returned by `indexed` do not satisfy the `std::input_or_output_iterator` concept.  
https://en.cppreference.com/w/cpp/iterator/input_or_output_iterator  
It is not immediately obvious why, so this is needs more digging. Technically, they should satisfy that, these iterator requirements are very basic.

---

## Comment 2

> Username: HDembinski  
> Created at: 2022-07-20 08:44:27 UTC  
> Url: https://github.com/boostorg/histogram/issues/355#issuecomment-1189999344  

It is not the iterator concept.  
```c++  
#include <ranges>  
#include <boost/histogram.hpp>  
  
template<std::input_or_output_iterator I>   
void test_iter(I) {}  
  
int main() {  
    using namespace boost::histogram;  
  
    auto hist = make_histogram(axis::integer(1, 2));  
  
    auto ind = indexed(hist);  
  
    // this works  
    test_iter(ind.begin());  
  
    // this fails  
    auto r2 = std::ranges::subrange(ind.begin(), ind.end());  
  
  return 0;  
}  
```

---

## Comment 3

> Username: HDembinski  
> Created at: 2022-07-20 08:55:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/355#issuecomment-1190010014  

Using indexed in ranges is currently not supported, it is untested. I will try to support this, since the syntax is nice and it makes sense to use an indexed in a range expression to strip the flow content.
