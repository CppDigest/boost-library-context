# #310 - [bug] weighted_mean variance calculation in += [Closed]

> Username: henryiii  
> Created at: 2021-03-16 14:08:33 UTC  
> Updated at: 2021-03-17 10:53:30 UTC  
> Closed at: 2021-03-17 10:53:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/310  

The same issue fixed in #308 with `mean` seems to be present in `weighted_mean`, as well:  
  
```cpp  
#include <boost/histogram.hpp>  
#include <boost/histogram/accumulators/ostream.hpp>  
#include <iostream>  
  
int main() {  
  namespace bh = boost::histogram;  
  
  bh::accumulators::weighted_mean a, b, c;  
  a(1); a(2); a(3);  
    
  b(5); b(6);  
    
  c(1); c(2); c(3); c(5); c(6);  
    
  auto d = a;  
  d += b;  
    
  std::cout << d << " " << c << " " << (a == b ? "Same" : "Different") << std::endl;  
}  
```  
  
```  
weighted_mean(5, 3.4, 0.625) weighted_mean(5, 3.4, 4.3) Different  
```

---

## Comment 1

> Username: henryiii  
> Created at: 2021-03-16 14:38:04 UTC  
> Updated at: 2021-03-16 18:49:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/310#issuecomment-800315223  

Unrelated: Why is `weighted_mean& operator*` not noexcept? Simple omission or intentional design?

---

## Comment 2

> Username: henryiii  
> Created at: 2021-03-17 04:37:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/310#issuecomment-800787728  

Exaclty the same fix fixes WeighedMean. I've fixed it in https://github.com/scikit-hep/boost-histogram/pull/537 - I can apply the same fix here if you'd like, @HDembinski, or you can do it, whatever you think is faster/easier to get in.

---

## Comment 3

> Username: HDembinski  
> Created at: 2021-03-17 06:55:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/310#issuecomment-800844880  

I suspected as much, but planned to implement this later.

---

## Comment 4

> Username: HDembinski  
> Created at: 2021-03-17 10:49:57 UTC  
> Updated at: 2021-03-17 10:53:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/310#issuecomment-800984436  

Taken care of in #311 also the missing noexcept that you found.
