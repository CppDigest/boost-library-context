# #886 - Dead link on the Advanced Topics page in the documentation. [Open]

> Username: fishnet37222  
> Created at: 2024-08-06 18:20:00 UTC  
> Updated at: 2025-02-10 01:48:34 UTC  
> Url: https://github.com/boostorg/compute/issues/886  

The "Advanced Topics" page in the documentation contains a link to a blog post that no longer exists.  The link on the page is ["Custom OpenCL functions in C++ with Boost.Compute"](http://kylelutz.blogspot.com/2014/03/custom-opencl-functions-in-c-with.html).  If you navigate to that link, you'll see it no longer exists.  However, I was able to find the blog post in the Internet Archive: [https://web.archive.org/web/20201112042827/http://kylelutz.blogspot.com/2014/03/custom-opencl-functions-in-c-with.html](https://web.archive.org/web/20201112042827/http://kylelutz.blogspot.com/2014/03/custom-opencl-functions-in-c-with.html).

---

## Comment 1

> Username: ljluestc  
> Created at: 2025-02-10 01:48:32 UTC  
> Url: https://github.com/boostorg/compute/issues/886#issuecomment-2646747596  

```  
  
#include <iostream>  
#include <vector>  
#include <boost/compute.hpp>  
  
namespace compute = boost::compute;  
  
int main() {  
    // Select the default OpenCL device  
    compute::device device = compute::system::default_device();  
    compute::context context(device);  
    compute::command_queue queue(context, device);  
  
    std::cout << "Using OpenCL device: " << device.name() << std::endl;  
  
    // OpenCL kernel source (custom function)  
    const char source[] = R"(  
        __kernel void square(__global float *data) {  
            int gid = get_global_id(0);  
            data[gid] = data[gid] * data[gid]; // Square each element  
        }  
    )";  
  
    // Build OpenCL program and create kernel  
    compute::program program = compute::program::build_with_source(source, context);  
    compute::kernel kernel(program, "square");  
  
    // Create input data  
    std::vector<float> host_data = { 1.0f, 2.0f, 3.0f, 4.0f, 5.0f };  
    compute::vector<float> device_data(host_data.size(), context);  
  
    // Copy data to device  
    compute::copy(host_data.begin(), host_data.end(), device_data.begin(), queue);  
  
    // Set kernel argument and execute  
    kernel.set_arg(0, device_data);  
    queue.enqueue_1d_range_kernel(kernel, 0, host_data.size(), 0);  
  
    // Copy results back to host  
    compute::copy(device_data.begin(), device_data.end(), host_data.begin(), queue);  
  
    // Print results  
    std::cout << "Squared Values:" << std::endl;  
    for (float val : host_data) {  
        std::cout << val << " ";  
    }  
    std::cout << std::endl;  
  
    return 0;  
}  
```
