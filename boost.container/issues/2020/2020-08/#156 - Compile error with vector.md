# #156 - Compile error with vector [Closed]

> Username: gshirreffs  
> Created at: 2020-08-12 06:37:24 UTC  
> Updated at: 2020-10-21 23:10:14 UTC  
> Closed at: 2020-10-21 23:10:14 UTC  
> Url: https://github.com/boostorg/container/issues/156  

With a custom allocator which specifies 16-bit size and difference types, the next_capacity calculation of vector causes an error:  
  
```  
#include <boost/container/vector.hpp>  
  
class CustomAllocator  
{  
public:  
	typedef int value_type;  
	typedef value_type* pointer;  
	typedef const value_type* const_pointer;  
	typedef unsigned short size_type;  
	typedef short difference_type;  
  
	pointer allocate(size_type count);  
	void deallocate(pointer ptr, size_type count);  
};  
  
int main()  
{  
	boost::container::vector<int, CustomAllocator> test;  
	test.push_back(1);  
	return 0;  
}  
  
```  
Compiling with Visual C++ 2019, the above gives an error at container/detail/next_capacity.hpp(56)  
  
'boost::container::dtl::max_value': no matching overloaded function found  
  
The issue is caused by cur_cap+min_cap automatically widening to int, which then fails to match any max_value implementation.  Adding a cast around the addition resolves the issue:  
  
      return max_value(SizeType(Minimum), max_value(SizeType(cur_cap+add_min_cap), min_value(max_cap, new_cap)));
