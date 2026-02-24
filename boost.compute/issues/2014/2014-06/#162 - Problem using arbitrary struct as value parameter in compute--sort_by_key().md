# #162 - Problem using arbitrary struct as value parameter in compute::sort_by_key() [Closed]

> Username: jesko42  
> Created at: 2014-06-30 14:32:39 UTC  
> Updated at: 2017-04-07 07:26:40 UTC  
> Closed at: 2017-04-07 07:26:40 UTC  
> Url: https://github.com/boostorg/compute/issues/162  

Hello Kyle,  
  
I tried to use compute::sort_by_key with a vector<float> as key and a struct like that  
  
```  
typedef struct  
{  
    float a;  
    float v[20];  
} imx;  
vector<imx>  
```  
  
as the value (or payload).  
But unfortunately I cannot use this struct, only basic data types like int, float, double or so ...  
Browsing the code it looks not too difficult to extend the current behaviour ...  
  
/Jesko  
  
```  
//--------------------------------------------------------- code snippet below  
    std::vector<float> host_vector_keys(33);  
    std::vector<imx> host_vector_payload(host_vector_keys.size());  
    compute::vector<float> device_vector_keys = host_vector_keys;  
    compute::vector<imx> device_vector_payload = host_vector_payload;  
    compute::sort_by_key(device_vector_keys.begin(), device_vector_keys.end(), device_vector_payload.begin());  
```  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-07-01 04:21:15 UTC  
> Updated at: 2014-07-01 04:21:31 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-47615566  

Hi Jesko,  
  
This should be possible. However, you will have to use the [`BOOST_COMPUTE_ADAPT_STRUCT`](http://kylelutz.github.io/compute/BOOST_COMPUTE_ADAPT_STRUCT.html) macro to tell Boost.Compute and OpenCL about your C++ struct.  
  
Let me know if you run into any issues.  
  
Cheers,  
Kyle

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-07-30 01:55:40 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-50564814  

@jesko42 I assume this is working for you. Please re-open if you are still having issues.

---

## Comment 3

> Username: jaytea  
> Created at: 2014-09-03 13:17:25 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-54294650  

Hi Kyle,  
sorry for reopening, but I seem to have this problem despite having used the BOOST_COMPUTE_ADAPT_STRUCT macro. It works just fine with a count_if and copy_if before trying to sort. Also, if I add the definition of the struct to the radix_sort_source and make sure that "struct" is added before the structs name with the following code in radix_sort.hpp everything works as expected.  
  
``` cpp  
if(sort_by_key){  
    options << " -DSORT_BY_KEY";  
    if( is_fundamental<T2>::value )  
        options << " -DT2=" << type_name<T2>();  
    else  
        options << " -DT2=\"struct " << type_name<T2>() << "\"";  
}  
```  
  
/jay

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-09-04 05:26:59 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-54409017  

Hmm, could you provide a small, compilable test-case/example which demonstrates the problem? This will help in tracking down the bug and getting it fixed.  
  
Thanks,  
Kyle

---

## Comment 5

> Username: jaytea  
> Created at: 2014-09-04 13:55:59 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-54480238  

Ok, this is strange. I tried to write a little example but was not able to reproduce the error. I will now try to distill my program down. I think the problem might be that I use a self-written wrapper for OpenCL and CUDA and use boost::compute only for algorithms like sort and scan. But maybe the build log for the sort might give you an idea whats wrong.  
  
```  
  
--- build log ---  
:63:32: error: unknown type name 'CellOnDevice'  
                      __global T2 *values_input,  
                               ^  
In file included from <built-in>:17306:  
<command line>:5:12: note: instantiated from:  
#define T2 CellOnDevice  
           ^  
:64:32: error: unknown type name 'CellOnDevice'  
                      __global T2 *values_output)  
                               ^  
In file included from <built-in>:17306:  
<command line>:5:12: note: instantiated from:  
#define T2 CellOnDevice  
           ^  
  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
  what():  Build Program Failure  
```

---

## Comment 6

> Username: kylelutz  
> Created at: 2014-09-04 14:55:46 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-54489309  

Hmm, it seems like it's not picking up the definition for the `CellOnDevice` struct. I'll look into it.  
  
And thanks, having a small example test-case that we can add to the unit tests would be very helpful in fixing this and ensuring it continues to work correctly in the future.  
  
Thanks!

---

## Comment 7

> Username: jaytea  
> Created at: 2014-09-04 15:17:34 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-54492867  

Ok, I got it! The problem really seems to be that I create all the OpenCL stuff myself. Here is the code that produces the error:  
  
```  
#include <vector>  
#include <algorithm>  
#include <boost/compute.hpp>  
  
  
struct CellOnDevice  
{  
    unsigned int index1;  
    unsigned int index2;  
  
    cl_short4 coords;  
};  
  
BOOST_COMPUTE_TYPE_NAME(cl_short4, short4);  
BOOST_COMPUTE_ADAPT_STRUCT(CellOnDevice, CellOnDevice, ( index1, index2, coords));  
  
int main()  
{  
    cl_device_id    device;  
    cl_context      context;  
    cl_command_queue  commandQueue;  
    int iErr;  
  
    int platformID = 2; // 2 = Nvidia  
  
    cl_platform_id platforms[platformID];  
    iErr = clGetPlatformIDs( platformID, platforms, NULL );  
    if( iErr != CL_SUCCESS )  
    {  
        std::cout << "Error getting OpenCL platform (" << iErr << ")" << std::endl;  
        return 0;  
    }  
  
    cl_platform_id platformInUse = platforms[platformID-1];  
  
    iErr = clGetDeviceIDs( platformInUse, CL_DEVICE_TYPE_DEFAULT, 1, &device, NULL );  
    if( iErr != CL_SUCCESS )  
    {  
        std::cout << "Error getting default OpenCL device (" << iErr << ")" << std::endl;  
        return 0;  
    }  
  
  
    // create the context  
    cl_context_properties properties[] =  
    {  
        CL_CONTEXT_PLATFORM, (cl_context_properties) platformInUse,  
        0  
    };  
  
    context = clCreateContext( properties, 1, &device, NULL, NULL, &iErr );  
    if( iErr != CL_SUCCESS )  
    {  
        std::cout << "Error creating the OpenCL context (" << ( iErr ) << ")" << std::endl;  
        return 0;  
    }  
  
    commandQueue = clCreateCommandQueue( context, device, 0, &iErr );  
    if( iErr != CL_SUCCESS )  
    {  
        std::cout << "Error creating an OpenCL command queue within the just created context (" << ( iErr ) << ")" << std::endl;  
        return 0;  
    }  
  
  
    boost::compute::command_queue queue( commandQueue );  
  
    // generate random numbers on the host  
    std::vector<float> host_vector(1000000);  
    std::generate(host_vector.begin(), host_vector.end(), rand);  
  
  
    std::vector<CellOnDevice> host_vector_values(1000000);  
    for( int i=0; i<1000000; ++i )  
    {  
        host_vector_values[i].index1 = i*10;  
        host_vector_values[i].index2 = 10;  
    }  
  
  
    // create vector on the device  
    cl_mem device_keys = clCreateBuffer( context, CL_MEM_READ_WRITE | CL_MEM_COPY_HOST_PTR, 1000000 * sizeof(float), (void*)&host_vector[0], &iErr );  
    cl_mem device_values = clCreateBuffer( context, CL_MEM_READ_WRITE | CL_MEM_COPY_HOST_PTR, 1000000 * sizeof(CellOnDevice), (void*)&host_vector_values[0], &iErr );  
    if( iErr != CL_SUCCESS )  
    {  
        std::cout << "Error creating an OpenCL buffer (" << ( iErr ) << ")" << std::endl;  
        return 0;  
    }  
  
    // sort data on the device  
    boost::compute::buffer bufKeys( device_keys );  
    boost::compute::buffer bufValues( device_values );  
  
    boost::compute::sort_by_key( boost::compute::make_buffer_iterator<unsigned int>( bufKeys, 0 ), boost::compute::make_buffer_iterator<unsigned int>( bufKeys, 1000000 ),  
                                    boost::compute::make_buffer_iterator<CellOnDevice>( bufValues, 0 ), queue );  
  
    // copy data back to the host  
    iErr = clEnqueueReadBuffer( commandQueue, device_keys, CL_TRUE, 0, 1000000 * sizeof(float), &host_vector[0], 0, NULL, NULL );  
    iErr = clEnqueueReadBuffer( commandQueue, device_values, CL_TRUE, 0, 1000000 * sizeof(CellOnDevice), &host_vector_values[0], 0, NULL, NULL );  
    if( iErr != CL_SUCCESS )  
        std::cout << "Error copying an OpenCL buffer to the host (" << ( iErr ) << ")" << std::endl;  
  
    return 0;  
}  
```

---

## Comment 8

> Username: jaytea  
> Created at: 2014-09-10 13:33:48 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-55115216  

Hi Kyle! Quick question, are you working on this or should I try to fix this one myself?   
Because now I have a problem which seems to be related to this and my crappy hacked fix.

---

## Comment 9

> Username: kylelutz  
> Created at: 2014-09-10 14:40:33 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-55124843  

I haven't had much time to work on this in the past week. If you have a fix I'd be very interested in getting it merged in (instructions for submitting pull-requests can be found in the [developers guide](https://github.com/kylelutz/compute/wiki/Developer's-Guide) on the wiki).  
  
Also, it would be good to have a small test case for this issue which could be added to the [`test_sort_by_key.cpp`](https://github.com/kylelutz/compute/blob/master/test/test_sort_by_key.cpp) file. Thanks!

---

## Comment 10

> Username: jaytea  
> Created at: 2014-09-10 14:48:02 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-55126145  

Ok, I will try my best. The current hacked "fix" is just the above mentioned inclusion of the structs definition in the source code for the radix sort. So now I'll try to get to the root of this.

---

## Comment 11

> Username: jaytea  
> Created at: 2014-09-17 16:30:53 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-55921110  

Ok, I don't know why, but even the following code does not work for me (crashes with the aforementioned error). Can you confirm this?  
  
```  
#include <vector>  
#include <algorithm>  
#include <boost/compute.hpp>  
  
#define SORT_SIZE 100000  
  
struct TestStruct  
{  
    unsigned int index1;  
    unsigned int index2;  
};  
  
BOOST_COMPUTE_ADAPT_STRUCT(TestStruct, TestStruct, ( index1, index2 ));  
  
namespace compute = boost::compute;  
int main()  
{  
    // get the default compute device  
    compute::device gpu = compute::system::default_device();  
  
    // create a compute context and command queue  
    compute::context ctx(gpu);  
    compute::command_queue queue(ctx, gpu);  
  
    std::vector<float> host_keys(SORT_SIZE);  
    std::vector<TestStruct> host_values(SORT_SIZE);  
  
    std::generate(host_keys.begin(), host_keys.end(), rand);  
    for( int i=0; i<SORT_SIZE; ++i )  
    {  
        host_values[i].index1 = rand();  
        host_values[i].index2 = 10*i;  
    }  
  
    compute::vector<float> device_keys( host_keys.begin(), host_keys.end(), queue );  
    compute::vector<TestStruct> device_values( host_values.begin(), host_values.end(), queue );  
  
    compute::sort_by_key( device_keys.begin(), device_keys.end(), device_values.begin(), queue );  
  
    // copy data back to the host  
    compute::copy(  
            device_values.begin(), device_values.end(), host_values.begin(), queue  
    );  
  
    return 0;  
}  
```

---

## Comment 12

> Username: kylelutz  
> Created at: 2014-09-18 14:54:41 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-56050284  

Yeah, that crashes for me too (fails to compile the OpenCL program). This is probably caused by the radix sort program using custom built program with its source as a raw string instead of using the `meta_kernel` infrastructure. I'll try to find some time to rewrite the algorithm using `meta_kernel`.

---

## Comment 13

> Username: jaytea  
> Created at: 2014-09-18 15:16:04 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-56053558  

Thanks, that would be awesome! Unfortunately I don't have much time at the moment due to some upcoming exams.

---

## Comment 14

> Username: jaytea  
> Created at: 2015-02-12 21:18:18 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-74154608  

Hi there,  
  
did you make any progress regarding this issue? Now I have to get some timings of my program and it would be nice to be able to use OpenCL for the sorting there.  
Best regards  
jay

---

## Comment 15

> Username: kylelutz  
> Created at: 2015-02-14 02:42:39 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-74358131  

Sorry, haven't made much progress on this. I've been busy with the peer-review process and getting the library ready for inclusion in Boost (which should hopefully happen soon). Getting this issue fixed is definitely still on my TODO list.

---

## Comment 16

> Username: jaytea  
> Created at: 2015-02-22 14:06:39 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-75436938  

Ok, that is a very good reason. I'll just use the cpu for sorting for now. Thanks for your work and good luck with the peer review.

---

## Comment 17

> Username: jszuppe  
> Created at: 2017-04-07 07:26:40 UTC  
> Url: https://github.com/boostorg/compute/issues/162#issuecomment-292462549  

Fixed by https://github.com/boostorg/compute/issues/701.
