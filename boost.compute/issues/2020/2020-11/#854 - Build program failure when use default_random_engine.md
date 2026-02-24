# #854 - Build program failure when use default_random_engine. [Closed]

> Username: kilasuelika  
> Created at: 2020-11-19 02:44:28 UTC  
> Updated at: 2020-11-19 03:00:13 UTC  
> Closed at: 2020-11-19 03:00:13 UTC  
> Url: https://github.com/boostorg/compute/issues/854  

I want to generate some random normal numbers. My code:  
```  
#include <iostream>  
#include <boost/compute.hpp>  
  
namespace compute = boost::compute;  
  
int main()  
{  
	// get the default device  
	compute::device device = compute::system::default_device();  
	compute::context context(device);  
	compute::command_queue queue(context, device);  
  
	// print the device's name and platform  
	std::cout << "hello from " << device.name();  
	std::cout << " (platform: " << device.platform().name() << ")" << std::endl;  
  
	compute::vector<float> vec(100, context);  
	compute::default_random_engine engine(queue);  
  
	// setup the uniform distribution to produce floats between 1 and 100  
	//compute::uniform_real_distribution<float> distribution(1.0, 100.0);  
	//distribution.generate(vec.begin(), vec.end(), engine, queue);  
	return 0;  
}  
```  
  
Error occurs at `compute::default_random_engine engine(queue);` on runtime. Saying:  
```  
terminate called after throwing an instance of 'boost::wrapexcept<boost::compute::program_build_failure>'  
  what():  Build Program Failure  
```   
  
I also tried `mersenne_twister_engine<int>`, no luck.

---

## Comment 1

> Username: kilasuelika  
> Created at: 2020-11-19 03:00:13 UTC  
> Url: https://github.com/boostorg/compute/issues/854#issuecomment-730094976  

By catch the error and print `build_log`, I realized it's because my GPU opencl version is 1.1 which is too low.
