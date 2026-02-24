# #674 - Convert-Error in reduce_by_key [Closed]

> Username: Ravi0li  
> Created at: 2016-12-05 11:45:36 UTC  
> Updated at: 2017-03-21 08:47:46 UTC  
> Closed at: 2017-03-21 08:47:46 UTC  
> Url: https://github.com/boostorg/compute/issues/674  

I wrote an example with a small struct, where I get a convert-error by the ```reduce_by_key``` function:  
```CPP  
#include <algorithm>  
#include <iostream>  
#include <vector>  
  
#include <boost/compute/system.hpp>  
#include <boost/compute/algorithm/copy.hpp>  
#include <boost/compute/algorithm/reduce_by_key.hpp>  
#include <boost/compute/container/vector.hpp>  
#include <boost/compute/types/struct.hpp>  
  
namespace compute = boost::compute;  
  
struct structTest  
{  
	int a;  
};  
BOOST_COMPUTE_ADAPT_STRUCT(structTest, structTest, (a));  
  
structTest rand_int()  
{  
	structTest ret;  
	ret.a = rand() % 100;  
	return ret;  
}  
  
BOOST_COMPUTE_FUNCTION(bool, Equal, (structTest lhs, structTest rhs),  
{  
    return (lhs.a < rhs.a);  
});  
  
BOOST_COMPUTE_FUNCTION(structTest, Add, (structTest lhs, structTest rhs),  
{  
    structTest ret;  
	ret.a = lhs.a + rhs.a;  
	return ret;  
});  
  
int main()  
{  
	// show devices  
	int nDevices = boost::compute::system::device_count();  
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
	std::string selection;  
	std::cout << "Device number: ";  
	std::cin >> selection;  
	device = devices[std::stoi(selection)];  
    boost::compute::context context = boost::compute::context(device);  
	boost::compute::command_queue queue = boost::compute::command_queue(context, device);  
	std::cout << device.name() << " -- " << device.version() << std::endl;  
	  
	// create vectors of random values on the host  
    std::vector<structTest> host_vector_val(100);  
	std::vector<structTest> host_vector_key(100);  
    std::generate(host_vector_val.begin(), host_vector_val.end(), rand_int);  
	std::generate(host_vector_key.begin(), host_vector_key.end(), rand_int);  
  
    // reserve on device  
	compute::vector<structTest> device_vector_val(host_vector_val,queue);  
	compute::vector<structTest> device_vector_key(host_vector_key,queue);  
	compute::vector<structTest> device_vector_val_res(context);  
	device_vector_val_res.reserve(device_vector_val.size());  
	compute::vector<structTest> device_vector_key_res(context);  
	device_vector_key_res.reserve(device_vector_key.size());  
  
	// Reduce by Key  
	try   
	{  
		compute::reduce_by_key(device_vector_key.begin(),  
							   device_vector_key.end(),  
							   device_vector_val.begin(),  
							   device_vector_key_res.begin(),  
							   device_vector_val_res.begin(),  
							   Add,  
							   Equal,  
							   queue);  
	}  
	catch (std::exception &ex)  
	{  
		std::cout << ex.what() << std::endl;  
		throw;  
	}  
  
	std::cout << "OK - end" << std::endl;  
	std::fflush(stdin);  
	std::getchar();  
  
    return 0;  
}  
```  
  
My program output is:  
```  
Device number: 0  
  Device name: Quadro K4000  
  OpenCL Version: OpenCL 1.2 CUDA  
  Vendor: NVIDIA Corporation  
  Global Memory Size: 3221225472  
Device number: 1  
  Device name: Intel(R) Xeon(R) CPU           X5550  @ 2.67GHz  
  OpenCL Version: OpenCL 2.1 (Build 18)  
  Vendor: Intel(R) Corporation  
  Global Memory Size: 21457240064  
Device number: 1  
Intel(R) Xeon(R) CPU           X5550  @ 2.67GHz -- OpenCL 2.1 (Build 18)  
3 errors generated.  
Boost.Compute: kernel compilation failed (-11)  
--- source ---  
#define boost_pair_type(t1, t2) _pair_ ## t1 ## _ ## t2 ## _t  
#define boost_pair_get(x, n) (n == 0 ? x.first ## x.second)  
#define boost_make_pair(t1, x, t2, y) (boost_pair_type(t1, t2)) { x, y }  
#define boost_tuple_get(x, n) (x.v ## n)  
typedef struct __attribute__((packed)) {  
    int a;  
} structTest;  
  
inline bool Equal(structTest lhs, structTest rhs){ return (lhs.a < rhs.a); }  
  
inline structTest Add(structTest lhs, structTest rhs){ structTest ret; ret.a = lhs.a + rhs.a; return ret; }  
  
  
__kernel void serial_reduce_by_key(uint count, __global uint* result_size, __global structTest* _buf0, __global structTest* _buf1, __global structTest* _buf2, _  
_global structTest* _buf3)  
{  
structTest result = convert_structTest(_buf0[0]);  
structTest previous_key = _buf1[0];  
structTest value;  
structTest key;  
uint size = 1;  
_buf2[0] = previous_key;  
_buf3[0] = result;  
for(ulong i = 1; i < count; i++) {  
    value = convert_structTest(_buf0[i]);  
    key = _buf1[i];  
    if (Equal(previous_key, key)) {  
        result = Add(result, value);  
    }  
     else {  
_buf2[size - 1] = previous_key;  
_buf3[size - 1] = result;  
        result = value;  
        size++;  
    }  
    previous_key = key;  
}  
_buf2[size - 1] = previous_key;  
_buf3[size - 1] = result;  
*result_size = size;  
}  
  
--- build log ---  
Compilation started  
1:16:21: error: implicit declaration of function 'convert_structTest' is invalid in OpenCL  
1:16:12: error: initializing 'structTest' with an expression of incompatible type 'int'  
1:24:11: error: assigning to 'structTest' from incompatible type 'int'  
Compilation failed  
  
Build Program Failure  
```  
When I exchange following lines in the ```serial_reduce_by_key``` function in ```serial_reduce_by_key.hpp```, it works on my GPU:  
```CPP  
// Line 62  
" = " << values_first[0] << ";\n" <<  
// Line 73  
"    value = " << values_first[k.var<uint_>("i")] << ";\n" <<  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-12-05 12:17:34 UTC  
> Url: https://github.com/boostorg/compute/issues/674#issuecomment-264840159  

Hm... You're right. That `covert_T()` operation was me begin overzealous. I was probably thinking about built-in vector types, but forgot about custom structures. We should remove `covert_T()` operation, it's unnecessary. I you like you can prepare pull-request for that fix. If not, I can do it later today.  
  
However, I think we should add a note somewhere about types requirement (basically copy types requirement from [std::accumulate](http://en.cppreference.com/w/cpp/algorithm/accumulate)).

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-03-21 08:47:46 UTC  
> Url: https://github.com/boostorg/compute/issues/674#issuecomment-288012327  

Fixed in https://github.com/boostorg/compute/pull/675
