# #600 - compute::copy returns incorrect values when template parameter mismatchs [Closed]

> Username: MossanTokyo  
> Created at: 2016-04-26 15:17:48 UTC  
> Updated at: 2016-06-03 15:04:00 UTC  
> Closed at: 2016-06-03 15:03:55 UTC  
> Url: https://github.com/boostorg/compute/issues/600  

When templates for `compute::vector<T> gpu_vec` and `std::vector<U> host_vec` mismatch, `compute::copy(gpu_vec.begin(),gpu_vec.end(),host_vec.begin(),queue)` returns incorrect values.  
  
I think it would be nice to throw an error or write a warning in documentation.  
  
The following code demonstrate the issue and was compiled under Visual Studio 2015 with the latest develop branch.  
  
```  
#include <boost/compute.hpp>  
#include<iostream>  
  
namespace compute = boost::compute;  
  
// select device  
compute::device select_device()  
{  
    auto devices = compute::system::devices();  
  
    int i = 0;  
    for (auto & elem : devices)  
    {  
        std::cout << "device[" << i++ << "]: " << elem.name() << std::endl;  
    }  
  
    int device_id;  
    std::cout << "select device: ";  
    std::cin >> device_id;  
  
    return devices[device_id];  
}  
  
int main()  
{  
    // select device  
    auto device = select_device();  
    compute::context context(device);  
    compute::command_queue queue(context, device);  
  
    //sample data  
    compute::vector<int> gpu_vec(5, context);  
  
  
    int start = 0;  
  
    // generate 0 to 4  
    compute::iota(gpu_vec.begin(), gpu_vec.end(), start, queue);  
  
  
    // gpu->host  
    // instead of std::vector<int>  
    std::vector<double> host_vec(5);  
  
    compute::copy(gpu_vec.begin(),gpu_vec.end(),host_vec.begin(),queue);  
  
    // check result  
    for (auto & elem : host_vec)  
    {  
        std::cout << elem << std::endl;  
    }  
  
    return 0;  
}  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-04-26 17:10:24 UTC  
> Url: https://github.com/boostorg/compute/issues/600#issuecomment-214814402  

You're right (see https://github.com/boostorg/compute/issues/363.), currently, `boost::compute::copy` just copies bytes.   
  
I have plans to fix this. It'll be done before the end of the May (it's in my Boost SoC schedule). Probably copying between device vectors of different types (but fundamental types) will perform conversion. The problem is when we need to copy data from host to the device (and the other way around), it requires additional memory (on host or on the device).

---

## Comment 2

> Username: pavanky  
> Created at: 2016-04-26 18:31:46 UTC  
> Updated at: 2016-04-26 18:31:52 UTC  
> Url: https://github.com/boostorg/compute/issues/600#issuecomment-214841598  

@haahh Shouldn't the function atleast error out when the data types are different for now?

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-04-26 18:46:16 UTC  
> Url: https://github.com/boostorg/compute/issues/600#issuecomment-214846120  

Well.. I'm thinking if there is any case anyone would like to copy host-allocated data of one type to the device-allocated vector of another type. I'm sure we need to allow copying `int`s to `int2` vector etc., but if there is some more "exotic" case. If not we can add some assert or `enable_if` statement to force copying only between related types (`int`, `int2`, ...).

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-05-17 18:30:16 UTC  
> Url: https://github.com/boostorg/compute/issues/600#issuecomment-219810533  

If you have any suggestions/questions about this problem with `boost::compute::copy` please post them in https://github.com/boostorg/compute/issues/363.

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-06-01 09:18:22 UTC  
> Url: https://github.com/boostorg/compute/issues/600#issuecomment-222938152  

Fixed in #616.
