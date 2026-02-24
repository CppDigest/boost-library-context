# #682 - vector of compute::command_queue [Closed]

> Username: ppacory  
> Created at: 2017-01-09 12:59:49 UTC  
> Updated at: 2017-01-09 13:17:48 UTC  
> Closed at: 2017-01-09 13:17:44 UTC  
> Url: https://github.com/boostorg/compute/issues/682  

is it possible to have a dynamic vector of compute::command_queue ?  
i've already try only a dynamic vector of compute::vector without problem.  
but with this code i've got a segmentation fault  
  
```  
#include <cstdint>  
#include <vector>  
#include <algorithm>  
#include <boost/compute.hpp>  
#include <boost/thread.hpp>  
  
namespace compute = boost::compute;  
  
int main()  
{  
  // get default device and setup context  
  compute::device device = compute::system::default_device();  
  compute::context context1(device);  
  
  int value=3; // create three vector  
  std::vector< compute::vector<uint64_t>* > device_vec(value); // a vector of compute::vector  
  std::vector< compute::command_queue* > queues; // a vector of compute::command_queue  
  
  // create three compute::vector on device  
  for (int i=0;i<value;i++)  
  {  
    device_vec[i] = new compute::vector<uint64_t> (100,context1);  
    queues[i] = new compute::command_queue (context1, device);  
    //compute::iota(device_vec[i]->begin(), device_vec[i]->end(), 0, queues[i]);  
  }  
  
  while (1) {}  
  
  return 0;  
}  
```

---

## Comment 1

> Username: ppacory  
> Created at: 2017-01-09 13:17:44 UTC  
> Url: https://github.com/boostorg/compute/issues/682#issuecomment-271281902  

find the error :  
```  
std::vector< compute::command_queue* > queues(value);  
```
