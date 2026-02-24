# #862 - Cannot run example on README.md: what():  Out of Host Memory [Closed]

> Username: KShChan  
> Created at: 2021-05-07 10:30:37 UTC  
> Updated at: 2021-05-11 10:37:06 UTC  
> Closed at: 2021-05-11 10:37:06 UTC  
> Url: https://github.com/boostorg/compute/issues/862  

Source code from README.md of this repo  
```  
#include <vector>  
#include <algorithm>  
#include <boost/compute.hpp>  
  
namespace compute = boost::compute;  
  
int main()  
{  
    // get the default compute device  
    compute::device gpu = compute::system::default_device();  
  
    // create a compute context and command queue  
    compute::context ctx(gpu);  
    compute::command_queue queue(ctx, gpu);  
  
    // generate random numbers on the host  
    std::vector<float> host_vector(1000000);  
    std::generate(host_vector.begin(), host_vector.end(), rand);  
  
    // create vector on the device  
    compute::vector<float> device_vector(1000000, ctx);  
  
    // copy data to the device  
    compute::copy(  
        host_vector.begin(), host_vector.end(), device_vector.begin(), queue  
    );  
  
    // sort data on the device  
    compute::sort(  
        device_vector.begin(), device_vector.end(), queue  
    );  
  
    // copy data back to the host  
    compute::copy(  
        device_vector.begin(), device_vector.end(), host_vector.begin(), queue  
    );  
  
    return 0;  
}  
```  
Output message  
```  
terminate called after throwing an instance of 'boost::wrapexcept<boost::compute::opencl_error>'  
  what():  Out of Host Memory  
```  
  
CPU: AMD Ryzen 7 3700X  
GPU: RX 6700XT 12GB  
RAM: 16GB  
IDE: CLion  
Toolchain: Cygwin

---

## Comment 1

> Username: KShChan  
> Created at: 2021-05-11 10:37:04 UTC  
> Url: https://github.com/boostorg/compute/issues/862#issuecomment-838243526  

Just use MingW64 to compile
