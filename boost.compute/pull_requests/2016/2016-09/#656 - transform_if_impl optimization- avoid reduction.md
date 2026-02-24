# #656 transform_if_impl optimization: avoid reduction [Merged]

> Username: Slonegg  
> Created at: 2016-09-10 21:27:12 UTC  
> Updated at: 2017-01-26 16:01:14 UTC  
> Merged at: 2016-10-19 04:08:09 UTC  
> Closed at: 2016-10-19 04:08:09 UTC  
> Url: https://github.com/boostorg/compute/pull/656  

I think it is faster this way, avoiding separate reduction.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-09-10 21:36:06 UTC  
> Url: https://github.com/boostorg/compute/pull/656#issuecomment-246141786  

Thanks! However, I think I would be better not to use `back()` method in the algorithm since it uses different command queue (and that command queue must be created). Instead, use the iterator to the last element and call its [`read(command_queue queue)` ](https://github.com/boostorg/compute/blob/master/include/boost/compute/iterator/buffer_iterator.hpp#L172) method.

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-09-10 22:34:38 UTC  
> Url: https://github.com/boostorg/compute/pull/656#issuecomment-246147944  

[![Coverage Status](https://coveralls.io/builds/7832030/badge)](https://coveralls.io/builds/7832030)  
  
Coverage increased (+0.006%) to 83.344% when pulling **4bd324e6ff368139b2f2d1b5eac55f50df0ffe2d on Slonegg:master** into **86211062a771ab0b494116b4223b63a292ad7d11 on boostorg:develop**.

---

## Comment 3

> Username: coveralls  
> Created_at: 2016-09-10 23:28:46 UTC  
> Url: https://github.com/boostorg/compute/pull/656#issuecomment-246151899  

[![Coverage Status](https://coveralls.io/builds/7832219/badge)](https://coveralls.io/builds/7832219)  
  
Coverage increased (+0.006%) to 83.344% when pulling **4bd324e6ff368139b2f2d1b5eac55f50df0ffe2d on Slonegg:master** into **86211062a771ab0b494116b4223b63a292ad7d11 on boostorg:develop**.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2016-09-14 03:15:17 UTC  
> Url: https://github.com/boostorg/compute/pull/656#issuecomment-246894187  

Thanks for the patch! Boost.Compute is currently in bug-fix-only mode until Boost 1.62 is released. Once that's out we can get this merged.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2016-10-19 04:08:10 UTC  
> Url: https://github.com/boostorg/compute/pull/656#issuecomment-254705495  

Merged! Thanks again!

---

## Comment 6

> Username: wierich  
> Created_at: 2017-01-25 16:21:56 UTC  
> Updated_at: 2017-01-26 16:01:14 UTC  
> Url: https://github.com/boostorg/compute/pull/656#issuecomment-275154418  

The changed file leads to errors on Intel CPUs if you try to reduce equal points in stl files:  
1. Intel(R) Xeon(R) CPU X5550 @ 2.67GHz  
2. Intel(R) Core(TM) i5-6400 CPU @ 2.70GHz  
3. Intel(R) Core(TM) i5-6442EQ CPU @ 1.90GHz  
4. Intel(R) Core(TM) i7-5600U CPU @ 2.60GHz  
  
In the following example, the points are reduced to 0 on Intel CPUs, but for example on AMD graphic card Sapphire Nitro Radeon(TM) R9 380X the points are reduced to 3670291 on my tested computers.  
Operating system is Windows 7 x64  
Compiler is VS 2012  
  
```cpp  
#include <boost/compute/algorithm/sort.hpp>  
#include <boost/compute/algorithm/unique.hpp>  
#include <random>  
  
const int NUMBER_OF_POINTS = 3679636;  
const int NUMBER_OF_EQUAL_POINTS = 6;  
  
struct Float3D  
{  
	float x;  
	float y;  
	float z;  
  
	Float3D(float _x, float _y, float _z) : x(_x), y(_y), z(_z) {};  
	Float3D() : x(0.0), y(0.0), z(0.0) {};  
};  
  
BOOST_COMPUTE_ADAPT_STRUCT(Float3D, Float3D, (x, y, z));  
  
BOOST_COMPUTE_FUNCTION(bool, Float3D_Equal, (Float3D l, Float3D r),  
{  
	return (l.x == r.x && l.y == r.y && l.z == r.z);  
});  
  
BOOST_COMPUTE_FUNCTION(bool, Float3D_Less, (Float3D l, Float3D r),  
{  
	if (l.x == r.x)  
	{  
		if (l.y == r.y)  
		{  
			return (l.z < r.z);  
		}  
		else  
			return (l.y < r.y);  
	}  
	else  
		return (l.x < r.x);  
});  
  
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
	std::string selection;  
	std::cout << "Device number: ";  
	std::cin >> selection;  
	device = devices[std::stoi(selection)];  
	boost::compute::context context = boost::compute::context(device);  
	boost::compute::command_queue queue = boost::compute::command_queue(context, device);  
  
	std::default_random_engine generator;  
	std::uniform_real_distribution<float> distribution(-5.0, 5.0);  
  
	// generate 3D data  
	std::vector<Float3D> different_vector;  
	different_vector.reserve(NUMBER_OF_POINTS);  
  
	for (int i = 0; i < NUMBER_OF_POINTS; i++)  
	{  
		different_vector.push_back(Float3D(distribution(generator), distribution(generator), distribution(generator)));  
	}  
  
	std::uniform_int_distribution<int> distribution_int(0, NUMBER_OF_POINTS);  
  
	std::vector<Float3D> data_vector;  
	data_vector.reserve(NUMBER_OF_POINTS * NUMBER_OF_EQUAL_POINTS);  
  
	for (int i = 0; i < NUMBER_OF_POINTS * NUMBER_OF_EQUAL_POINTS; i++)  
	{  
		data_vector.push_back(different_vector[distribution_int(generator)]);  
	}  
  
	// remove equal points  
	boost::compute::vector<Float3D> device_data_vector(data_vector, queue);  
  
	size_t size_begin = device_data_vector.size();  
  
	boost::compute::sort(device_data_vector.begin(), device_data_vector.end(), Float3D_Less, queue);  
  
	auto newEnd = boost::compute::unique(device_data_vector.begin(), device_data_vector.end(), Float3D_Equal, queue);   
	device_data_vector.resize(newEnd - device_data_vector.begin());  
  
	std::cout << device_data_vector.size() << " of " << size_begin << " points left" << std::endl;  
  
	return 0;  
}  
```  
  
The error does not occure with some other value for NUMBER_OF_POINTS.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2017-01-25 19:29:39 UTC  
> Url: https://github.com/boostorg/compute/pull/656#issuecomment-275208120  

Interesting, thanks for the report. @Slonegg, can you look into this? We may have to revert before the next release if this breaks Intel platforms.

---

## Comment 8

> Username: Slonegg  
> Created_at: 2017-01-25 19:54:45 UTC  
> Url: https://github.com/boostorg/compute/pull/656#issuecomment-275214937  

Not until weekends for sure @kylelutz but if it is driver issue, maybe it's worth submitting this to Intel devs. Maybe add workaround like command queue flush or something to workaround issue on Intel.

---
