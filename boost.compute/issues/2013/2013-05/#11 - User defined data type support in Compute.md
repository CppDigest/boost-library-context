# #11 - User defined data type support in Compute. [Closed]

> Username: ravibanger  
> Created at: 2013-05-18 09:42:24 UTC  
> Updated at: 2015-02-08 19:46:08 UTC  
> Closed at: 2014-03-09 04:09:53 UTC  
> Url: https://github.com/boostorg/compute/issues/11  

Hi,  
    I created a simple test case for sorting of user defined data types. but it throws a compilation error as shown below.   
    Do you plan to support User defined data types. Or are all the algorithms in compute restricted to OpenCL basic data types.   
Am i missing something in the below code.   
Regards,  
Banger.   
  
/*********_Code Snippet**_*/  
# include <algorithm>  
# include <iostream>  
# include <vector>  
# include <boost/compute.hpp>  
  
class UDD  
{  
public:  
    int a;   
    int b;   
    float c;  
    bool operator()    (const UDD &lhs, const UDD &rhs)  
    {  
        return lhs.a < rhs.a;  
    }  
};  
  
UDD rand_UDD()  
{  
    UDD temp;  
    temp.a = rand() % 100;  
    temp.b = rand() % 100;  
    temp.c = (float)(rand() % 100) / 1.3;  
    return temp;  
}  
  
// this example demonstrates how to sort a vector of ints on the GPU  
int main()  
{  
    // create vector of random values on the host  
    std::vector<UDD> host_vector(10);  
    std::generate(host_vector.begin(), host_vector.end(), rand_UDD);  
    // transfer the values to the device  
    boost::compute::vector<UDD> device_vector = host_vector;  
  
```  
// sort the values on the device  
boost::compute::sort(device_vector.begin(), device_vector.end());  
```  
  
}

---

## Comment 1

> Username: ravibanger  
> Created at: 2013-05-18 10:00:02 UTC  
> Updated at: 2013-05-18 11:17:57 UTC  
> Url: https://github.com/boostorg/compute/issues/11#issuecomment-18098219  

I did take a deeper look into the algorithm which is implemented for sort. You seem to be calling radix-sort for device_vector size greater than 32. And hence a compilation error. You might want to specialize radix sort based on the data type.   
One the other hand AMD's Bolt   
https://github.com/HSA-Libraries/Bolt/  
 library can evaluate and support user defined data types. But it uses the C++ static kernel specification, which is an AMD only standard.

---

## Comment 2

> Username: kylelutz  
> Created at: 2013-05-21 01:48:41 UTC  
> Url: https://github.com/boostorg/compute/issues/11#issuecomment-18184587  

Hi Ravishekhar,  
  
Thanks for reporting this issue.  
  
I am still working on better ways to integrate user-defined data types with the Boost.Compute algorithms. As of now, the best way to do this is to use `boost::tuple` to define your data-types. As for sorting by the first component you can use a lambda expression for the `compare` argument to `boost::compute::sort()`.  I've added an example showing how to do this here: https://github.com/kylelutz/compute/blob/master/test/test_issue_11.cpp.  
  
Cheers,  
Kyle

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-03-09 04:09:53 UTC  
> Url: https://github.com/boostorg/compute/issues/11#issuecomment-37118581  

I've added support (dec92cc438574625422e0486d22c5d43a624a44c) for adapting user-defined structs/classes from C++ to OpenCL.   
  
The new [BOOST_COMPUTE_ADAPT_STRUCT()](http://kylelutz.github.io/compute/BOOST_COMPUTE_ADAPT_STRUCT.html) macro allows for C++ data-types to be used in OpenCL with Boost.Compute containers and algorithms.  
  
I've updated the test ([test_issue_11.cpp](https://github.com/kylelutz/compute/blob/master/test/test_issue_11.cpp)) to use the new macro. Have a look and let me know if you run into any issues.
