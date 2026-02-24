# #305 - bug?: possible surprising results for inclusive on integer axes? [Closed]

> Username: henryiii  
> Created at: 2020-12-22 21:21:03 UTC  
> Updated at: 2021-04-24 10:24:47 UTC  
> Closed at: 2021-04-24 10:24:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/305  

[Compiling the following program](https://wandbox.org/permlink/NRUMVhRvZ6eXL0mL):  
  
```cpp  
#include <iostream>  
#include <boost/histogram.hpp>  
  
  
namespace bh = boost::histogram;  
  
int main() {  
  
    bh::axis::integer<> x1(0, 4);  
    std::cout << "integer default: " <<  (bh::axis::traits::inclusive(x1) ? "True" : "False") << std::endl;  
      
    bh::axis::integer<int, bh::use_default, bh::axis::option::overflow_t> x2(0, 4);  
    std::cout << "integer overflow only: " <<  (bh::axis::traits::inclusive(x2) ? "True" : "False") << std::endl;  
      
    bh::axis::integer<int, bh::use_default, bh::axis::option::underflow_t> x3(0, 4);  
    std::cout << "integer underflow only: " <<  (bh::axis::traits::inclusive(x3) ? "True" : "False") << std::endl;  
      
    bh::axis::integer<int, bh::use_default, bh::axis::option::none_t> x4(0, 4);  
    std::cout << "integer noflow: " <<  (bh::axis::traits::inclusive(x4) ? "True" : "False") << std::endl;  
      
    bh::axis::regular<> x5(4, 0, 4);  
    std::cout << "regular default: " << (bh::axis::traits::inclusive(x5) ? "True" : "False") << std::endl;  
      
    bh::axis::regular<double, bh::use_default, bh::use_default, bh::axis::option::overflow_t> x6(4, 0, 4);  
    std::cout << "regular overflow only: " << (bh::axis::traits::inclusive(x6) ? "True" : "False") << std::endl;  
      
    bh::axis::regular<double, bh::use_default, bh::use_default, bh::axis::option::underflow_t> x7(4, 0, 4);  
    std::cout << "regular underflow only: " <<  (bh::axis::traits::inclusive(x7) ? "True" : "False") << std::endl;  
      
    bh::axis::regular<double, bh::use_default, bh::use_default, bh::axis::option::none_t> x8(4, 0, 4);  
    std::cout << "regular noflow: " <<  (bh::axis::traits::inclusive(x8) ? "True" : "False") << std::endl;  
}  
  
  
```  
  
Gives this result:  
  
```  
integer default: True  
integer overflow only: True  
integer underflow only: True  
integer noflow: False  
regular default: True  
regular overflow only: False  
regular underflow only: False  
regular noflow: False  
```  
  
It seems `integer` axis with only one flow bin is still "inclusive", unlike Regular (and Variable, though not shown above). Is this expected?  
  
PS: If you start with integer, and then try regular, sadly this compiles correctly: `bh::axis::regular<double, bh::use_default, bh::axis::option::overflow_t>` - but is obviously wrong - the options are in the metadata slot. Don't know if there's a way to protect or print a warning if this happens, as anything is supported for metadata.

---

## Comment 1

> Username: henryiii  
> Created at: 2020-12-22 21:28:56 UTC  
> Updated at: 2020-12-22 21:29:27 UTC  
> Url: https://github.com/boostorg/histogram/issues/305#issuecomment-749784404  

This seems to say that the default is to only be inclusive if both underflow and overflow is set:  
  
https://github.com/boostorg/histogram/blob/ff188c61f66844ad4907d128197feb3c8d0e5fe9/include/boost/histogram/axis/traits.hpp#L52  
  
But, when customized for Integer,  
  
https://github.com/boostorg/histogram/blob/ff188c61f66844ad4907d128197feb3c8d0e5fe9/include/boost/histogram/axis/integer.hpp#L156-L158  
  
Then `options() & option::underflow || options() & option::overflow` is true if either one is set. I think it should be `options() & option::underflow && options() & option::overflow`, perhaps?

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-12-26 16:23:56 UTC  
> Url: https://github.com/boostorg/histogram/issues/305#issuecomment-751371406  

Right, looks like a bug, thank you for reporting.

---

## Comment 3

> Username: HDembinski  
> Created at: 2020-12-26 16:26:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/305#issuecomment-751371604  

It looks like I did this on purpose and not by mistake, though, hm. I should have left a comment.

---

## Comment 4

> Username: HDembinski  
> Created at: 2021-04-24 10:14:20 UTC  
> Updated at: 2021-04-24 10:17:06 UTC  
> Url: https://github.com/boostorg/histogram/issues/305#issuecomment-826070021  

> PS: If you start with integer, and then try regular, sadly this compiles correctly: bh::axis::regular<double, bh::use_default, bh::axis::option::overflow_t> - but is obviously wrong - the options are in the metadata slot. Don't know if there's a way to protect or print a warning if this happens, as anything is supported for metadata.  
  
Yes, a safer order would have been `value_type, metadata, options, transform` for the template arguments, but I chose to put the transform as a second argument instead, which I thought is more user-friendly. I will try to add some static_assert to check the passed types. This will be much nicer with concepts in the future.  
  
Edit: On closer inspection, I think there is nothing I can do here to avoid this. Since metadata is allowed to be anything, I cannot really catch this.

---

## Comment 5

> Username: HDembinski  
> Created at: 2021-04-24 10:17:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/305#issuecomment-826070379  

@henryiii Did this bug affect you downstream?
