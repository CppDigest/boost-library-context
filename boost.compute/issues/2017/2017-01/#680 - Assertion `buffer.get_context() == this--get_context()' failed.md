# #680 - Assertion `buffer.get_context() == this->get_context()' failed [Closed]

> Username: ppacory  
> Created at: 2017-01-02 12:59:56 UTC  
> Updated at: 2017-01-02 19:59:48 UTC  
> Closed at: 2017-01-02 19:59:46 UTC  
> Url: https://github.com/boostorg/compute/issues/680  

here the code :    
  
  
#include <cstdint>  
#include <vector>  
#include <algorithm>  
#include <boost/compute.hpp>  
//#include <boost/thread.hpp>  
  
  
namespace compute = boost::compute;  
  
int main()  
{  
  // get default device and setup context  
  compute::device device = compute::system::default_device();  
  compute::context context1(device);  
  compute::command_queue queue1(context1, device);  
  
  int value;  
  std::cout << "enter value : ";  
  std::cin >> value;  
  std::vector< std::vector<uint64_t>* > vec(value);  
  std::vector< compute::vector<uint64_t>* > device_vec(value);  
  compute::vector<uint64_t> temp(value);  
  for (int i=0;i<value;i++)  
  {  
    vec[i] = new std::vector<uint64_t>(3000);  
    device_vec[i] = new compute::vector<uint64_t>(3000);  
    std::generate(vec[i]->begin(), vec[i]->end(), rand);  
    compute::copy(vec[i]->begin(), vec[i]->end(), device_vec[i]->begin(), queue1);  
  }  
  return 0;  
}  
  
no error on compilation  
error on running :   
include/boost/compute/command_queue.hpp:435: boost::compute::event boost::compute::command_queue::enqueue_write_buffer(const boost::compute::buffer&, size_t, size_t, const void*, const boost::compute::wait_list&): Assertion `buffer.get_context() == this->get_context()' failed.

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-01-02 13:05:35 UTC  
> Url: https://github.com/boostorg/compute/issues/680#issuecomment-269970109  

Here:  
```cpp  
// get default device and setup context  
compute::device device = compute::system::default_device();  
compute::context context1(device);  
compute::command_queue queue1(context1, device);  
```  
 you create context `context1` and command queue `queue1` in that context , and here:  
```cpp  
for (int i=0;i<value;i++)  
{  
vec[i] = new std::vector<uint64_t>(3000);  
device_vec[i] = new compute::vector<uint64_t>(3000);  
std::generate(vec[i]->begin(), vec[i]->end(), rand);  
compute::copy(vec[i]->begin(), vec[i]->end(), device_vec[i]->begin(), queue1);  
}  
```  
you create `new compute::vector<uint64_t>(3000)` using default OpenCL context, not in your `context1`. That's why later you get this runtime error: context used to create `queue1` is different than context used to created your device vectors. You need to pass `context1` when you create vector.
