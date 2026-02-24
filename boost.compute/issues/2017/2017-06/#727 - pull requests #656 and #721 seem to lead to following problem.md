# #727 - pull requests #656 and #721 seem to lead to following problem [Closed]

> Username: wierich  
> Created at: 2017-06-02 12:45:47 UTC  
> Updated at: 2017-06-19 07:01:51 UTC  
> Closed at: 2017-06-19 07:01:51 UTC  
> Url: https://github.com/boostorg/compute/issues/727  

Pull request #656 and #721 lead to errors on Intel CPUs if you try to reduce equal points in stl files:  
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
  
	std::uniform_int_distribution<int> distribution_int(0, NUMBER_OF_POINTS - 1);  
  
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

## Comment 1

> Username: jszuppe  
> Created at: 2017-06-02 12:59:46 UTC  
> Url: https://github.com/boostorg/compute/issues/727#issuecomment-305778600  

Which result is correct?

---

## Comment 2

> Username: wierich  
> Created at: 2017-06-02 19:13:30 UTC  
> Updated at: 2017-06-02 19:17:11 UTC  
> Url: https://github.com/boostorg/compute/issues/727#issuecomment-305884723  

At the moment I can not say whether 3670291 or 3679636 is correct, but 0 is definitely wrong.

---

## Comment 3

> Username: jszuppe  
> Created at: 2017-06-02 19:18:36 UTC  
> Url: https://github.com/boostorg/compute/issues/727#issuecomment-305885840  

I'll look at it tomorrow. I don't know how my fix cause this problem. I was  
sure that without it scan was failing on Intel CPUs due to accessing out of  
bounds memory.  
  
02.06.2017 9:13 PM "wierich" <notifications@github.com> napisał(a):  
  
> 3670291 is correct.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/compute/issues/727#issuecomment-305884723>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACmCBP3vKEw9u8Wc4H_PaOKYi1oupA2Pks5sAF7cgaJpZM4NuO7w>  
> .  
>

---

## Comment 4

> Username: wierich  
> Created at: 2017-06-02 19:38:10 UTC  
> Url: https://github.com/boostorg/compute/issues/727#issuecomment-305890183  

I do not know if it is your fault. Maybe the problem results from another circumstance. When I return back to the situation before pull request #656 AND #721 the problem does not occur. That is the only hint I can give to you.

---

## Comment 5

> Username: jszuppe  
> Created at: 2017-06-02 19:39:56 UTC  
> Url: https://github.com/boostorg/compute/issues/727#issuecomment-305890539  

Ok. Thanks. I'll investigate tomorrow morning.  
  
02.06.2017 9:38 PM "wierich" <notifications@github.com> napisał(a):  
  
> I do not know if it is your fault. Maybe the problem results from another  
> circumstance. When I return back to the situation before pull request #656  
> <https://github.com/boostorg/compute/pull/656> AND #721  
> <https://github.com/boostorg/compute/pull/721> the problem does not  
> occur. That is the only hint I can give to you.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/compute/issues/727#issuecomment-305890183>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACmCBEZ0FeQzJ7ijeOkxHWeAS7yykcPyks5sAGSkgaJpZM4NuO7w>  
> .  
>

---

## Comment 6

> Username: jszuppe  
> Created at: 2017-06-03 11:31:00 UTC  
> Updated at: 2017-06-03 11:34:20 UTC  
> Url: https://github.com/boostorg/compute/issues/727#issuecomment-305969234  

I think I've found the bug. Please check https://github.com/jszuppe/compute/tree/fix_727 branch:  
  
```  
git fetch https://github.com/jszuppe/compute.git fix_727:fix_727  
git checkout fix_727  
```  
I run your code both on my GTX1080, Intel Core i7-6700K the result was: `3670933 of 22077816 points left`. I get the same result if I force `unique` to use serial algorithm,  you can try it by changing `unique_copy` function in [unique_copy.hpp](https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/unique_copy.hpp#L134) (line 134).

---

## Comment 7

> Username: jszuppe  
> Created at: 2017-06-07 09:02:12 UTC  
> Url: https://github.com/boostorg/compute/issues/727#issuecomment-306735202  

@wierich Can you confirm that the fix helps?

---

## Comment 8

> Username: wierich  
> Created at: 2017-06-08 05:46:42 UTC  
> Url: https://github.com/boostorg/compute/issues/727#issuecomment-307004972  

Thank you for your help!  
On monday 19.06. I am back in the office to check if your fix helps.

---

## Comment 9

> Username: wierich  
> Created at: 2017-06-19 07:01:51 UTC  
> Url: https://github.com/boostorg/compute/issues/727#issuecomment-309355567  

@jszuppe Your fix helps. Thank you again.
