# #687 - Exception on Intel CPUs using reduce_by_key function with int vectors [Closed]

> Username: wierich  
> Created at: 2017-01-27 16:02:16 UTC  
> Updated at: 2017-05-15 15:08:08 UTC  
> Closed at: 2017-05-15 15:08:08 UTC  
> Url: https://github.com/boostorg/compute/issues/687  

I am pretty sure that this is an Intel bug, but I could not find the correct intel page to submit the issue.  
The exception occurs in the following example only on Intel CPUs and not on Intel GPUs:  
1. Intel(R) Core(TM) i5-6442EQ CPU @ 1.90 GHz -- OpenCL 2.0 (Build 370)  
2. Intel(R) Xeon(R) CPU X5550 @ 2.67GHz -- OpenCL 1.2 (Build 25)  
3. Intel(R) Xeon(R) CPU X5550 @ 2.67GHz -- OpenCL 2.1 (Build 18)  
4. Intel(R) Core(TM) i5-6400 CPU @ 2.70GHz -- OpenCL 2.0 (Build 10094)  
  
If I choose Intel(R) Core(TM) i5-6400 device with "OpenCL 1.2 AMD-APP(2117.14)" OpenCL Version, then the example succeeds! (On that computer I also installed drivers for an AMD graphics card.)  
  
Operating system is Windows 7 / 8.1  
Compiler is VS 2012  
  
```cpp  
#include <boost/compute/algorithm/reduce_by_key.hpp>  
  
int rand_int()  
{  
    return rand() % 100;  
}  
  
int main()  
{  
	// show devices  
	int i=0;  
	for (const auto &device : boost::compute::system::devices())  
	{  
		std::cout << "Device number: " << i << std::endl;  
		std::cout << "  Device name: " << device.name() << std::endl;  
		std::cout << "  OpenCL Version: " << device.version() << std::endl;  
		std::cout << "  Vendor: " << device.vendor() << std::endl;  
		std::cout << "  Global Memory Size: " << device.global_memory_size() << std::endl;  
		i++;  
	}  
  
	// select device  
	boost::compute::device device;  
	std::vector<boost::compute::device> devices = boost::compute::system::devices();  
	std::cout << "Device number: ";  
	std::string selection;  
	std::cin >> selection;  
	device = devices[std::stoi(selection)];  
	boost::compute::context context = boost::compute::context(device);  
	boost::compute::command_queue queue = boost::compute::command_queue(context, device);  
	std::cout << device.name() << " -- " << device.version() << std::endl;  
  
	// create vectors of random values on the host  
	std::vector<int> host_vector_val(100);  
	std::vector<int> host_vector_key(100);  
	std::generate(host_vector_val.begin(), host_vector_val.end(), rand_int);  
	std::generate(host_vector_key.begin(), host_vector_key.end(), rand_int);  
  
	// reserve on device  
	boost::compute::vector<int> device_vector_val(host_vector_val,queue);  
	boost::compute::vector<int> device_vector_key(host_vector_key,queue);  
	boost::compute::vector<int> device_vector_val_res(context);  
	device_vector_val_res.reserve(device_vector_val.size());  
	boost::compute::vector<int> device_vector_key_res(context);  
	device_vector_key_res.reserve(device_vector_key.size());  
  
	// Reduce by Key  
	try   
	{  
		boost::compute::reduce_by_key(device_vector_key.begin(),  
								device_vector_key.end(),  
								device_vector_val.begin(),  
								device_vector_key_res.begin(),  
								device_vector_val_res.begin(),  
								boost::compute::plus<int>(),  
								boost::compute::equal_to<int>(),  
								queue);  
	}  
	catch (std::exception &ex)  
	{  
		std::cout << ex.what() << std::endl;  
	}  
  
	return 0;  
}  
```  
  
The exception occurs in file `boost/compute/algorithm/detail/serial_reduce_by_key.hpp` in the function `boost::compute::detail::serial_reduce_by_key` in the line 99 `return static_cast<size_t>(result_size.read(queue));` when the memory of `scalar<uint_> result_size(context);` in line 93 is being freed. Here is the exception stack trace  
```  
ntdll.dll!00007ff975bf8c5b()	Unbekannt  
ntdll.dll!00007ff975bfbe36()	Unbekannt  
ntdll.dll!00007ff975bfca34()	Unbekannt  
ntdll.dll!00007ff975bb2047()	Unbekannt  
intelocl64.dll!00007ff952b03bef()	Unbekannt  
intelocl64.dll!00007ff952af1fc6()	Unbekannt  
intelocl64.dll!00007ff952af2494()	Unbekannt  
intelocl64.dll!00007ff952af4312()	Unbekannt  
intelocl64.dll!00007ff952af1e04()	Unbekannt  
intelocl64.dll!00007ff952af2434()	Unbekannt  
intelocl64.dll!00007ff952a9ceb9()	Unbekannt  
intelocl64.dll!00007ff952a6fc0a()	Unbekannt  
OpenCLTest.exe!boost::compute::memory_object::~memory_object() Zeile 238	C++  
OpenCLTest.exe!boost::compute::buffer::~buffer() Zeile 125	C++  
OpenCLTest.exe!boost::compute::detail::scalar<unsigned int>::~scalar<unsigned int>() Zeile 36	C++  
OpenCLTest.exe!boost::compute::detail::serial_reduce_by_key<boost::compute::buffer_iterator<int>,boost::compute::buffer_iterator<int>,boost::compute::buffer_iterator<int>,boost::compute::buffer_iterator<int>,boost::compute::plus<int>,boost::compute::equal_to<int> >(boost::compute::buffer_iterator<int> keys_first, boost::compute::buffer_iterator<int> keys_last, boost::compute::buffer_iterator<int> values_first, boost::compute::buffer_iterator<int> keys_result, boost::compute::buffer_iterator<int> values_result, boost::compute::plus<int> function, boost::compute::equal_to<int> predicate, boost::compute::command_queue & queue) Zeile 99	C++  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-03-25 12:51:34 UTC  
> Url: https://github.com/boostorg/compute/issues/687#issuecomment-289209849  

I confirm it seems to be a driver bug. I was not able to recreate it on POCL (CPU), AMD (CPU and GPU), and NVIDIA (GPU) platforms. I can't figure out what's causing the bug.

---

## Comment 2

> Username: jmr1  
> Created at: 2017-05-14 09:40:10 UTC  
> Url: https://github.com/boostorg/compute/issues/687#issuecomment-301301312  

This is bug in boost compute whereby vector::reserve() isn't implemented...  
I've implemented reserve() mimicking stl version.

---

## Comment 3

> Username: jszuppe  
> Created at: 2017-05-14 15:59:48 UTC  
> Url: https://github.com/boostorg/compute/issues/687#issuecomment-301321981  

Oh, I don't notice that there were reserve() methods called in the example. Thanks for fixing it. I'll test your change this week.

---

## Comment 4

> Username: jszuppe  
> Created at: 2017-05-15 15:08:07 UTC  
> Url: https://github.com/boostorg/compute/issues/687#issuecomment-301504578  

Fixed by https://github.com/boostorg/compute/issues/722.
