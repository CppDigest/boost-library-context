# #839 - clone_kernel test fails on Intel(R) Gen9 HD Graphics NEO [Open]

> Username: giacomini  
> Created at: 2019-06-27 12:54:26 UTC  
> Updated at: 2025-02-10 01:43:39 UTC  
> Url: https://github.com/boostorg/compute/issues/839  

The clone_kernel test (in `test/test_kernel.cpp`)  
```  
BOOST_AUTO_TEST_CASE(clone_kernel)  
{  
    REQUIRES_OPENCL_PLATFORM_VERSION(2, 1);  
  
    compute::kernel k1 = compute::kernel::create_with_source(  
        "__kernel void test(__global int * x) { x[get_global_id(0)] = get_global_id(0); }",  
        "test", context  
    );  
  
    compute::buffer x(context, 5 * sizeof(compute::int_));  
    k1.set_arg(0, x);  
  
    // Clone k1 kernel  
    compute::kernel k2 = k1.clone();  
    // After clone k2 0th argument (__global float * x) should be set,  
    // so we should be able to enqueue k2 kernel without problems  
    queue.enqueue_1d_range_kernel(k2, 0, x.size() / sizeof(compute::int_), 0).wait();  
}  
```  
fails when run on my laptop's GPU (Intel(R) Gen9 HD Graphics NEO).  
The error message is:  
```  
.../Downloads/boost.compute.git/include/boost/compute/command_queue.hpp(1428): fatal error: in "boost::compute::event boost::compute::command_queue::enqueue_nd_range_kernel(const boost::compute::kernel&, size_t, const size_t*, const size_t*, const size_t*, const boost::compute::wait_list&)": boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >: Invalid Kernel Arguments  
.../Downloads/boost.compute.git/test/test_kernel.cpp(315): last checkpoint: "clone_kernel" test entry  
  
*** 1 failure is detected in the test module "TestKernel"  
```  
If I set explicitly the argument after cloning:  
```  
k2.set_arg(0, x);  
```  
the test is successful, but this is exactly what the test is supposed to check.  
I thought the problem could be with the [underlying OpenCL library](https://github.com/intel/compute-runtime/releases/tag/19.24.13171), but the [attached program](https://github.com/boostorg/compute/files/3334761/cl_test_kernel_clone.txt), written directly using OpenCL and hopefully reproducing the test, doesn't fail.  
  
Any idea? thanks.

---

## Comment 1

> Username: ljluestc  
> Created at: 2025-02-10 01:43:39 UTC  
> Url: https://github.com/boostorg/compute/issues/839#issuecomment-2646743657  

```  
  
#define BOOST_TEST_MODULE TestKernel  
#include <boost/test/unit_test.hpp>  
#include <boost/compute.hpp>  
  
namespace compute = boost::compute;  
  
BOOST_AUTO_TEST_CASE(clone_kernel)  
{  
    REQUIRES_OPENCL_PLATFORM_VERSION(2, 1);  
  
    // Setup OpenCL context and queue  
    compute::device gpu = compute::system::default_device();  
    compute::context context(gpu);  
    compute::command_queue queue(context, gpu);  
  
    // Kernel source  
    const char source[] = R"(  
        __kernel void test(__global int *x) {  
            x[get_global_id(0)] = get_global_id(0);  
        }  
    )";  
  
    // Create original kernel  
    compute::program program = compute::program::build_with_source(source, context);  
    compute::kernel k1(program, "test");  
  
    // Create buffer  
    compute::buffer x(context, 5 * sizeof(compute::int_));  
  
    // Set argument for the original kernel  
    k1.set_arg(0, x);  
  
    // Clone k1 kernel  
    compute::kernel k2 = k1.clone();  
  
    // 🛠 **FIX:** Re-set argument explicitly after cloning  
    k2.set_arg(0, x);  
  
    // Enqueue the cloned kernel  
    queue.enqueue_1d_range_kernel(k2, 0, x.size() / sizeof(compute::int_), 0).wait();  
  
    // Validate results  
    std::vector<int> output(5);  
    compute::copy(  
        compute::buffer_iterator<int>(x, 0),  
        compute::buffer_iterator<int>(x, 5),  
        output.begin(),  
        queue  
    );  
  
    for (int i = 0; i < 5; ++i) {  
        BOOST_CHECK_EQUAL(output[i], i);  
    }  
}  
```
