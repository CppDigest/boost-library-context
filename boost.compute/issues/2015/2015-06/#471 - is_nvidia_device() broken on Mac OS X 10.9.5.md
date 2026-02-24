# #471 - is_nvidia_device() broken on Mac OS X 10.9.5 [Closed]

> Username: msuchard  
> Created at: 2015-06-24 21:33:58 UTC  
> Updated at: 2015-07-08 04:01:16 UTC  
> Closed at: 2015-07-08 04:01:05 UTC  
> Url: https://github.com/boostorg/compute/issues/471  

The following code:  
  
``` c++  
#include <iostream>  
  
#include <boost/compute/core.hpp>  
#include <boost/compute/detail/vendor.hpp>  
  
namespace compute = boost::compute;  
  
int main()  
{  
    // get the default device  
    compute::device device = compute::system::default_device();  
  
    // print the device's vendor  
    std::cout << device.vendor() << std::endl;  
  
    std::cout << compute::detail::is_nvidia_device(device) << std::endl;  
  
    return 0;  
}  
```  
  
produces  
  
```  
NVIDIA  
0  
```  
  
since  
  
``` c++  
return device.vendor() == "NVIDIA Corporation"  
```  
  
fails in `vendor.hpp`.  Am happy to provide a pull-request fix.  
  
best, Marc

---

## Comment 1

> Username: pavanky  
> Created at: 2015-06-24 21:37:32 UTC  
> Url: https://github.com/boostorg/compute/issues/471#issuecomment-115019457  

Perhaps the default device on your machine is the intel GPU?

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-07-08 04:01:16 UTC  
> Url: https://github.com/boostorg/compute/issues/471#issuecomment-119417401  

Fixed in PR #472.
