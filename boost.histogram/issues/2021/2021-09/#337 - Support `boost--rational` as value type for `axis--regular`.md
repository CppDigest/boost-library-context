# #337 - Support `boost::rational` as value type for `axis::regular` [Open]

> Username: HDembinski  
> Created at: 2021-09-27 11:14:13 UTC  
> Updated at: 2022-11-15 11:24:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/337  

```cpp  
#include <boost/histogram.hpp>  
#include <boost/rational.hpp>  
  
namespace bh = boost::histogram;  
  
int main() {  
    using R = bh::axis::regular<boost::rational<int>>;  
  
    auto h = bh::make_histogram(R(10, 0, 1));  
}  
```  
  
This currently triggers a static_assert, but should be supported.

---

## Comment 1

> Username: HDembinski  
> Created at: 2021-09-27 11:14:36 UTC  
> Url: https://github.com/boostorg/histogram/issues/337#issuecomment-927767323  

Related #336

---

## Comment 2

> Username: HDembinski  
> Created at: 2022-01-10 13:10:40 UTC  
> Updated at: 2022-01-10 13:12:27 UTC  
> Url: https://github.com/boostorg/histogram/issues/337#issuecomment-1008859610  

Prototype for experimenting  
```c++  
#include <boost/histogram.hpp>  
#include <boost/histogram/ostream.hpp>  
#include <boost/rational.hpp>  
#include <iostream>  
  
int main() {  
    namespace bh = boost::histogram;  
  
    struct axis {  
        using value_type = boost::rational<int>;  
        using index_type = bh::axis::index_type;  
  
        index_type num_;  
        value_type offset_;  
        value_type scale_;  
  
        axis(index_type n, value_type a, value_type b) :  
            num_{n}, offset_{a}, scale_{rat{n} / (b - a)} {}  
  
        index_type index(const value_type& x) const {  
            const auto i = boost::rational_cast<index_type>(scale_ * (x - offset_));  
            return i >= -1 ? (i < num_ ? i : num_) : -1;  
        }  
  
        value_type value(index_type i) const {  
            return i / scale_ + offset_;  
        }  
  
        index_type size() const { return num_; }  
  
        static constexpr auto options() { return bh::axis::option::underflow | bh::axis::option::overflow; }  
    };  
  
    auto h = bh::make_histogram(axis{10, 0, 5});  
  
    h(-1);  
    h(1);  
    h(2);  
  
    std::cout << h << std::endl;  
}  
```
