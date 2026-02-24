# #419 - Issue when using just Intel OpenCL 1.2 SDK [Closed]

> Username: tunacode  
> Created at: 2015-01-30 06:04:53 UTC  
> Updated at: 2017-03-21 12:14:39 UTC  
> Closed at: 2017-03-21 12:14:39 UTC  
> Url: https://github.com/boostorg/compute/issues/419  

The following float vector sorting example works fine out of the box but the moment I try to create an empty OpenCV matrix, the example crashes even though the matrix is not being used anywhere. Is there any compatibility issue? My machine specs:  
  
Intel Core i3 CPU  
NVIDIA GeForce 610M GPU (but **NO** drivers or CUDA toolkit installed)  
Ubuntu 14.04  
Intel OpenCL 1.2 SDK installed  
  
Example code:  
  
``` c++  
#include <vector>  
#include <iostream>  
#include <algorithm>  
#include <boost/compute.hpp>  
#include <opencv2/highgui/highgui.hpp>  
  
using namespace std;  
namespace compute = boost::compute;  
  
int main()  
{  
    //Creating an empty Mat object does not settle well with boost::compute since it fails with an exception that no device found.  
    //If the line below is commented out, everything works fine. Some conflict?  
    cv::Mat m;  
  
    // get the default compute device  
    compute::device gpu = compute::system::default_device();  
  
    // create a compute context and command queue  
    compute::context ctx(gpu);  
    compute::command_queue queue(ctx, gpu);  
  
    // generate random numbers on the host  
    std::vector<float> host_vector(1000000);  
    std::generate(host_vector.begin(), host_vector.end(), rand);  
  
    // create vector on the device  
    compute::vector<float> device_vector(1000000, ctx);  
  
    // copy data to the device  
    compute::copy(  
        host_vector.begin(), host_vector.end(), device_vector.begin(), queue  
    );  
  
    // sort data on the device  
    compute::sort(  
        device_vector.begin(), device_vector.end(), queue  
    );  
  
    // copy data back to the host  
    compute::copy(  
        device_vector.begin(), device_vector.end(), host_vector.begin(), queue  
    );  
  
    return 0;  
}  
```  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-01-30 06:17:22 UTC  
> Url: https://github.com/boostorg/compute/issues/419#issuecomment-72159153  

That's very strange. Could you compile with debug symbols enabled (e.g. `gcc -g test.cpp`) and post a back-trace from the crash?

---

## Comment 2

> Username: salmanulhaq  
> Created at: 2015-01-30 06:48:52 UTC  
> Url: https://github.com/boostorg/compute/issues/419#issuecomment-72161294  

It basically fails to create a context since it fails to find any device. Here's the exact build and exception:  
  
``` sh  
userx@systemx:~/workspace/pxm/sandbox$ g++ -g -o test_basic_compute test_basic_compute.cpp -I/opt/intel/intel-opencl-1.2-4.6.0.92/opencl-sdk/include -I/usr/local/include/compute -I/usr/include -L/usr/lib/x86_64-linux-gnu -L/usr/local/lib/x86_64-linux-gnu -L/usr/lib -L/usr/local/lib -lOpenCL -lopencv_core -lopencv_highgui  
  
userx@systemx:~/workspace/pxm/sandbox$ ./test_basic_compute  
test_basic_compute: /usr/local/include/compute/boost/compute/context.hpp:63: boost::compute::context::context(const boost::compute::device&, const cl_context_properties*): Assertion `device.id() != 0' failed.  
Aborted (core dumped)  
```

---

## Comment 3

> Username: kylelutz  
> Created at: 2015-02-02 03:35:32 UTC  
> Url: https://github.com/boostorg/compute/issues/419#issuecomment-72402452  

Strange that it can find any devices. I know OpenCV has an optional OpenCL support module, I wonder if linking against that causes a different `libOpenCL.so` to be found instead of the Intel OpenCL library. Could you try looking at the `ldd` output from your binary before and after using OpenCV and see if the `libOpenCL.so` found changes? Perhaps using `LD_PRELOAD` to force Intels `libOpenCL.so` will fix this.

---

## Comment 4

> Username: salmanulhaq  
> Created at: 2015-02-03 07:38:31 UTC  
> Updated at: 2015-02-03 07:38:40 UTC  
> Url: https://github.com/boostorg/compute/issues/419#issuecomment-72607179  

I suspected that too but I already hard link to the same OpenCL lib. I double checked. The first one below is built without any OpenCV and the second one is built with OpenCV (just a simple cv::Mat declaration, nothing else):  
  
``` sh  
userx@systemx:~/workspace/pxm/sandbox$ ldd -v test_basic_compute | grep OpenCL  
    libOpenCL.so.1 => /usr/lib/x86_64-linux-gnu/libOpenCL.so.1 (0x00007f079ee19000)  
        libOpenCL.so.1 (OPENCL_2.0) => /usr/lib/x86_64-linux-gnu/libOpenCL.so.1  
        libOpenCL.so.1 (OPENCL_1.0) => /usr/lib/x86_64-linux-gnu/libOpenCL.so.1  
        libOpenCL.so.1 (OPENCL_1.2) => /usr/lib/x86_64-linux-gnu/libOpenCL.so.1  
    /usr/lib/x86_64-linux-gnu/libOpenCL.so.1:  
  
userx@systemx:~/workspace/pxm/sandbox$ ldd -v test_basic_compute | grep OpenCL  
    libOpenCL.so.1 => /usr/lib/x86_64-linux-gnu/libOpenCL.so.1 (0x00007f71afc2a000)  
        libOpenCL.so.1 (OPENCL_1.2) => /usr/lib/x86_64-linux-gnu/libOpenCL.so.1  
        libOpenCL.so.1 (OPENCL_2.0) => /usr/lib/x86_64-linux-gnu/libOpenCL.so.1  
        libOpenCL.so.1 (OPENCL_1.0) => /usr/lib/x86_64-linux-gnu/libOpenCL.so.1  
    /usr/lib/x86_64-linux-gnu/libOpenCL.so.1:  
```

---

## Comment 5

> Username: jszuppe  
> Created at: 2015-09-13 20:32:41 UTC  
> Url: https://github.com/boostorg/compute/issues/419#issuecomment-139915111  

@salmanulhaq I think https://github.com/boostorg/compute/pull/508 fixed it. There was a problem when default device was a CPU. Could you try it now using the `develop` branch?

---

## Comment 6

> Username: salmanulhaq  
> Created at: 2015-09-14 03:10:10 UTC  
> Url: https://github.com/boostorg/compute/issues/419#issuecomment-139949314  

awesome! I'll try it out later.

---

## Comment 7

> Username: jszuppe  
> Created at: 2017-03-21 12:14:39 UTC  
> Url: https://github.com/boostorg/compute/issues/419#issuecomment-288060519  

I'm closing it, please reopen if the bug was not fixed by #508.
