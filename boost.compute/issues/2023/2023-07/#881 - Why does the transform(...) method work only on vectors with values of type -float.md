# #881 - Why does the transform(...) method work only on vectors with values of type <float> ? [Open]

> Username: JohnMcLaren  
> Created at: 2023-07-16 02:32:26 UTC  
> Updated at: 2025-02-10 01:46:15 UTC  
> Url: https://github.com/boostorg/compute/issues/881  

My test program (Qt/C++):  
```c++  
#include <QCoreApplication>  
#include <QDebug>  
  
#include <boost/compute/core.hpp>  
#include <boost/compute/container/vector.hpp>  
#include <boost/compute/algorithm/transform.hpp>  
  
namespace cl = boost::compute;  
//----------------------------------  
int main(int argc, char *argv[])  
{  
QCoreApplication a(argc, argv);  
  
    // get the default device / context / queue  
cl::device gpu =cl::system::default_device();  
cl::context ctx(gpu);  
cl::command_queue queue(ctx, gpu);  
  
    qDebug("Device: %s", gpu.name().c_str());  
    qDebug("OpenCL Version: %s", gpu.version().c_str());  
    qDebug("Driver Version: %s", gpu.driver_version().c_str());  
    qDebug("Freq: %u | Units: %u | Memory: %lu", gpu.clock_frequency(), gpu.compute_units(), gpu.max_memory_alloc_size());  
  
    qDebug() << "[ Work.. ]";  
  
    // generate random data on the host  
std::vector<float> host_data(10000);  
std::generate(host_data.begin(), host_data.end(), rand);  
  
    for(int c =0; c < 5; ++c)  
        qDebug("%f", host_data[c]);  
  
// host_data[2] *=(-1); // surprise  
  
    qDebug() << "--- Host results (double) ---";  
  
    for(int c =0; c < 5; ++c)  
        qDebug("%f", std::sqrt((double)host_data[c]));  
  
    // create a vector on the device  
cl::vector<float> gpu_data(host_data.size(), ctx);  
  
    // transfer data from the host to the device  
    cl::copy(host_data.begin(), host_data.end(), gpu_data.begin(), queue);  
  
    // calculate the square-root of each element in-place  
    cl::transform(gpu_data.begin(), gpu_data.end(), gpu_data.begin(),  
        cl::sqrt<float>(), queue);  
  
    // copy values back to the host  
    cl::copy(gpu_data.begin(), gpu_data.end(), host_data.begin(), queue);  
  
    qDebug() << "--- GPU results ---";  
  
    for(int c =0; c < 5; ++c)  
        qDebug("%f", host_data[c]);  
  
    qDebug() << "[ DONE ]";  
//return a.exec();  
}  
```  
It's a normal output:  
```  
Device: Intel(R) Iris(R) Xe Graphics  
OpenCL Version: OpenCL 3.0 NEO   
Driver Version: 23.17.26241.21  
Freq: 1300 | Units: 96 | Memory: 4294959104  
[ Work.. ]  
1804289408.000000  
846930880.000000  
1681692800.000000  
1714636928.000000  
1957747840.000000  
--- Host results (double) ---  
42476.927949  
29102.076902  
41008.447910  
41408.174652  
44246.444377  
--- GPU results ---  
42476.929688  
29102.076172  
41008.449219  
41408.175781  
44246.445313  
[ DONE ]  
```  
But if I try to perform actions on a GPU vector of any type other than `<float>`,  
for example  
```c++  
    cl::vector<double> gpu_data(...);  
```  
or  
```c++  
    cl::vector<uint> gpu_data(...);  
```  
then the `transform` method fails:  
```  
--- Host results (double) ---  
42476.927949  
29102.076902  
41008.447910  
41408.174652  
44246.444377  
terminate called after throwing an instance of 'boost::wrapexcept<boost::compute::program_build_failure>'  
  what():  Build Program Failure  
```  
And it concerns not only root calculation.   
For example, the `floor()` rounding function also fails.  
  
Some tests with combinations of types:  
```c++  
/*** Work ***/  
std::vector<float> host_data(...);  
cl::vector<float> gpu_data(...);  
... cl::sqrt<float>(), ...);  
  
std::vector<float> host_data(...);  
cl::vector<float> gpu_data(...);  
... cl::sqrt<double>(), ...);  
  
std::vector<double> host_data(...);  
cl::vector<float> gpu_data(...);  
... cl::sqrt<double>(), ...);  
  
std::vector<double> host_data(...);  
cl::vector<float> gpu_data(...);  
... cl::sqrt<float>(), ...);  
  
/*** CL crash ***/  
std::vector<double> host_data(...);  
cl::vector<double> gpu_data(...);  
... cl::sqrt<double>(), ...);  
  
std::vector<double> host_data(...);  
cl::vector<double> gpu_data(...);  
... cl::sqrt<float>(), ...);  
  
std::vector<float> host_data(...);  
cl::vector<double> gpu_data(...);  
... cl::sqrt<double>(), ...);  
  
std::vector<float> host_data(...);  
cl::vector<double> gpu_data(...);  
... cl::sqrt<float>(), ...);  
```  
Is my GPU the problem? Is it Boost? OpenCL?

---

## Comment 1

> Username: ljluestc  
> Created at: 2025-02-10 01:46:14 UTC  
> Url: https://github.com/boostorg/compute/issues/881#issuecomment-2646745794  

```  
  
#include <QCoreApplication>  
#include <QDebug>  
#include <vector>  
#include <cmath>  
#include <algorithm>  
  
#include <boost/compute/core.hpp>  
#include <boost/compute/container/vector.hpp>  
#include <boost/compute/algorithm/transform.hpp>  
#include <boost/compute/algorithm/copy.hpp>  
  
namespace cl = boost::compute;  
  
int main(int argc, char *argv[]) {  
    QCoreApplication app(argc, argv);  
  
    try {  
        // Get the default OpenCL device, context, and queue  
        cl::device gpu = cl::system::default_device();  
        cl::context ctx(gpu);  
        cl::command_queue queue(ctx, gpu);  
  
        // Print OpenCL device info  
        qDebug() << "Device:" << QString::fromStdString(gpu.name());  
        qDebug() << "OpenCL Version:" << QString::fromStdString(gpu.version());  
        qDebug() << "Driver Version:" << QString::fromStdString(gpu.driver_version());  
        qDebug() << "Freq:" << gpu.clock_frequency()  
                 << "| Units:" << gpu.compute_units()  
                 << "| Memory:" << gpu.max_memory_alloc_size() / (1024 * 1024) << "MB";  
  
        qDebug() << "[ Working on OpenCL device... ]";  
  
        // Generate random float data on the host  
        std::vector<float> host_data(10000);  
        std::generate(host_data.begin(), host_data.end(), []() { return (rand() % 10000) / 100.0f; });  
  
        // Print first 5 values before processing  
        qDebug() << "[ Host Data (before transformation) ]";  
        for (int i = 0; i < 5; ++i)  
            qDebug() << "host_data[" << i << "] =" << host_data[i];  
  
        // Handle negative values (avoid sqrt(-x) issue)  
        for (auto &val : host_data) {  
            if (val < 0) val = 0;  // Convert negatives to zero  
        }  
  
        // Create a vector on the device  
        cl::vector<float> gpu_data(host_data.size(), ctx);  
  
        // Transfer data from host to device  
        cl::copy(host_data.begin(), host_data.end(), gpu_data.begin(), queue);  
  
        // Compute square-root on the GPU  
        cl::transform(gpu_data.begin(), gpu_data.end(), gpu_data.begin(), cl::sqrt<float>(), queue);  
  
        // Copy results back to the host  
        cl::copy(gpu_data.begin(), gpu_data.end(), host_data.begin(), queue);  
  
        // Print first 5 results  
        qDebug() << "[ Host Data (after GPU sqrt transformation) ]";  
        for (int i = 0; i < 5; ++i)  
            qDebug() << "host_data[" << i << "] =" << host_data[i];  
  
    } catch (const cl::opencl_error &e) {  
        qDebug() << "OpenCL Error:" << e.what();  
        return EXIT_FAILURE;  
    }  
  
    return app.exec();  
}  
```
