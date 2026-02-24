# #327 - Potential bug in dynamic fill [Closed]

> Username: HDembinski  
> Created at: 2021-06-23 08:36:28 UTC  
> Updated at: 2021-09-26 16:31:36 UTC  
> Closed at: 2021-09-26 16:31:36 UTC  
> Url: https://github.com/boostorg/histogram/issues/327  

See https://github.com/scikit-hep/boost-histogram/issues/589  
  
Needs to be investigated

---

## Comment 1

> Username: henryiii  
> Created at: 2021-09-14 15:34:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/327#issuecomment-919266656  

Reproducer:  
  
```cpp  
#include <boost/histogram.hpp>  
#include <boost/histogram/ostream.hpp>  
#include <iostream>  
#include <vector>  
  
namespace bh      = boost::histogram;  
using uogrowth_t = decltype(bh::axis::option::growth | bh::axis::option::underflow | bh::axis::option::overflow);  
  
template <typename T>  
using c_array_t = std::vector<T>;  
  
using arg_t = boost::variant2::variant<c_array_t<double>,  
                            double,  
                            c_array_t<int>,  
                            int,  
                            c_array_t<std::string>,  
                            std::string>;  
  
int main() {  
    using axis_type_1 = bh::axis::  
        regular<double, bh::use_default, bh::use_default, uogrowth_t >;  
    using axis_type_2  = bh::axis::regular<double>;  
    using axis_type_3  = bh::axis::integer<int>;  
    using axis_type_4  = bh::axis::category<int>;  
    using axis_type_5  = bh::axis::category<std::string>;  
    using axis_variant = bh::axis::  
        variant<axis_type_1, axis_type_2, axis_type_3, axis_type_4, axis_type_5>;  
  
    auto axes_orig = std::vector<axis_variant>({axis_type_1(10, 0, 1)});  
    auto h    = bh::histogram<std::vector<axis_variant>>(axes_orig);  
  
    std::vector<int> val = {-1};  
    auto args = val; // std::vector<std::vector<int>>({val}); // using this instead removes the crash  
  
    const auto& axes = bh::unsafe_access::axes(h);  
    auto vargs = bh::detail::make_stack_buffer<arg_t>(axes);  
  
    bh::detail::for_each_axis(  
        axes,  
        [args_it = args.begin(), vargs_it = vargs.begin()](const auto& ax) mutable {  
            using T = bh::axis::traits::value_type<std::decay_t<decltype(ax)>>;  
            // T is one of: int, double, std::string  
  
            const auto& x = *args_it++;  
            auto& v       = *vargs_it++;  
  
            v = x;  
        });  
  
    h.fill(vargs);  // CRASH, using h.fill(-1) or h.fill(args) does not crash.  
  
    std::cout << h << std::endl;  
    return 0;  
}  
```  
  
The crash only happens if `call_1(true_type, ...)` is called, the working paths all call `call_1(false_type, ...)`.

---

## Comment 2

> Username: henryiii  
> Created at: 2021-09-14 20:49:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/327#issuecomment-919501941  

`begin_` and `it` do not point at related memory, causing this  
https://github.com/boostorg/histogram/blob/4a10c2c11b272f435f61233eb9f585604476b2f8/include/boost/histogram/detail/fill_n.hpp#L82-L85  
to loop several times until it tries to overwrite something that causes the segfault. These lines do not run for positive growth.

---

## Comment 3

> Username: henryiii  
> Created at: 2021-09-14 22:03:00 UTC  
> Updated at: 2021-09-14 22:03:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/327#issuecomment-919544366  

> `auto args = val; // std::vector<std::vector<int>>({val}); // using this instead removes the crash`  
  
I think the problem is that single values do not work, but arrays do (for negative growth only). So if this is not a `c_array_t`, this fails, if it is, it works.

---

## Comment 4

> Username: HDembinski  
> Created at: 2021-09-16 13:27:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/327#issuecomment-920902201  

Thank you for investigating this. I am not sure why we weren't able to reproduce it before? I will try to implement the fix ASAP.

---

## Comment 5

> Username: HDembinski  
> Created at: 2021-09-16 13:35:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/327#issuecomment-920908381  

This looks like it is caused by the ugly handling of the special case of 1D histograms. In general, `fill` expects a container of containers, but for 1D histograms we want to accept also a single container.

---

## Comment 6

> Username: henryiii  
> Created at: 2021-09-16 13:38:33 UTC  
> Updated at: 2021-09-16 13:41:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/327#issuecomment-920910865  

I *think* this is because we can accept a single value or a container. That is, for a 2D histogram, filling with `{{1,2,3}, 9}` will fill 1,9 and 2,9 and 3,9, without needing to duplicate the 9. But that's not being handled by the lower-bound growing fill path correctly. But I haven't checked with a 2D histogram to see if that remains true.
