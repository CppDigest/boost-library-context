# #449 - Support for opencl vector types in kernel::set_arg [Closed]

> Username: ddemidov  
> Created at: 2015-04-05 11:28:31 UTC  
> Updated at: 2015-04-05 18:05:16 UTC  
> Closed at: 2015-04-05 18:05:13 UTC  
> Url: https://github.com/boostorg/compute/issues/449  

Currently, compiling the following code:  
  
``` cpp  
#include <boost/compute/core.hpp>  
  
int main() {  
    cl_float2 p;  
  
    boost::compute::kernel k;  
    k.set_arg(0, p);  
}  
```  
  
results in compiler error:  
  
```  
g++ -c hello.cpp  -I/home/demidov/work/compute/include  
In file included from /home/demidov/work/compute/include/boost/compute/memory_object.hpp:16:0,  
                 from /home/demidov/work/compute/include/boost/compute/buffer.hpp:17,  
                 from /home/demidov/work/compute/include/boost/compute/core.hpp:18,  
                 from hello.cpp:1:  
/home/demidov/work/compute/include/boost/compute/kernel.hpp: In instantiation of ‘struct boost::compute::detail::set_kernel_arg<cl_float2>’:  
/home/demidov/work/compute/include/boost/compute/kernel.hpp:245:56:   required from ‘void boost::compute::kernel::set_arg(size_t, const T&) [with T = cl_float2; size_t = long unsigned int]’  
hello.cpp:7:19:   required from here  
/home/demidov/work/compute/include/boost/compute/kernel.hpp:374:5: error: no type named ‘type’ in ‘struct boost::enable_if<boost::compute::is_fundamental<cl_float2>, void>’  
     operator()(kernel &kernel_, size_t index, const T &value)  
     ^  
/home/demidov/work/compute/include/boost/compute/kernel.hpp: In instantiation of ‘void boost::compute::kernel::set_arg(size_t, const T&) [with T = cl_float2; size_t = long unsigned int]’:  
hello.cpp:7:19:   required from here  
/home/demidov/work/compute/include/boost/compute/kernel.hpp:245:56: error: no match for call to ‘(boost::compute::detail::set_kernel_arg<cl_float2>) (boost::compute::kernel&, size_t&, const cl_float2&)’  
         detail::set_kernel_arg<T>()(*this, index, value);  
                                                        ^  
```  
  
I understand that there are types like `boost::compute::float2_`, but having the ability to use standard OpenCL types would be beneficial for interoperability with existing codes or other libraries. Btw, I hit the error while implementing Boost.Compute backend for VexCL.

---

## Comment 1

> Username: ddemidov  
> Created at: 2015-04-05 18:05:13 UTC  
> Url: https://github.com/boostorg/compute/issues/449#issuecomment-89822287  

Fixed in #450
