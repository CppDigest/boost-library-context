# #406 - Could the axis method `index` be templated? [Closed]

> Username: emmenlau  
> Created at: 2025-01-31 09:47:46 UTC  
> Updated at: 2025-06-05 21:14:23 UTC  
> Closed at: 2025-06-05 21:14:23 UTC  
> Url: https://github.com/boostorg/histogram/issues/406  

I have seen that boost::histogram will not accept a class for an axis where `index` is a template method. I considered that it would be helpful to have the parameter `x` be a template type for certain operations. Is there a strong reason not to allow this?

---

## Comment 1

> Username: HDembinski  
> Created at: 2025-06-05 13:48:11 UTC  
> Url: https://github.com/boostorg/histogram/issues/406#issuecomment-2944457116  

There is indeed a reason for this. boost::histogram does a lot of template meta-programming behind the scenes, and to support general objects that follow the [Axis concept](https://www.boost.org/doc/libs/latest/libs/histogram/doc/html/histogram/concepts.html#histogram.concepts.Axis), we need Axis::index to be a real method, because the generic detection code does not work for templated methods.  
  
Something that might work in your use-case, however, is to provide a concrete implementation of Axis::index, and then additionally overloads that are templated.

---

## Comment 2

> Username: HDembinski  
> Created at: 2025-06-05 14:03:27 UTC  
> Updated at: 2025-06-05 14:12:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/406#issuecomment-2944565267  

No, using a templated overload doesn't work, but here is a workaround that does work:  
  
```cpp  
#include <boost/histogram.hpp>  
#include <boost/histogram/ostream.hpp>  
#include <iostream>  
  
using namespace boost::histogram;  
  
struct my_weird_type {  
    double x;  
};  
  
struct my_axis {  
    struct index_arg {  
        int data_;  
        // put overloads to handle multiple data types here or use a template  
        index_arg(const my_weird_type& x) : data_{static_cast<int>(x.x)} {}  
        index_arg(const int& x) : data_{x} {}  
    };  
  
    int index(index_arg x) const { return x.data_; }  
    double value(int i) const { return static_cast<double>(i); }  
    int size() const { return 10; }  
};  
  
// Type your code here, or load an example.  
int main() {  
    auto h = make_histogram(my_axis());  
  
    my_weird_type x{5.2};  
  
    h(x);  
    h(4);  
  
    std::cout << h << std::endl;  
  
    return 0;  
}  
```  
  
Tested on godbolt.

---

## Comment 3

> Username: HDembinski  
> Created at: 2025-06-05 14:23:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/406#issuecomment-2944706349  

I will add this to the examples and update the documentation of the axis concept to point this out.
