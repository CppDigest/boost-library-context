# #850 - Fails to compile in Ubuntu 20 [Closed]

> Username: gavox  
> Created at: 2020-08-05 14:09:39 UTC  
> Updated at: 2020-08-06 15:55:42 UTC  
> Closed at: 2020-08-06 15:55:42 UTC  
> Url: https://github.com/boostorg/compute/issues/850  

Hello!  
  
Thanks for the good work. I am trying to learn to use the library, and have found issues. I am running the example at: https://www.boost.org/doc/libs/1_73_0/libs/compute/doc/html/boost_compute/tutorial.html. I run into the following error:   
  
```  
g++ bcholamundo.cpp  
In file included from /usr/include/CL/cl.h:32,  
                 from /usr/include/boost/compute/cl.hpp:19,  
                 from /usr/include/boost/compute/config.hpp:16,  
                 from /usr/include/boost/compute/buffer.hpp:14,  
                 from /usr/include/boost/compute/core.hpp:18,  
                 from bcholamundo.cpp:3:  
/usr/include/CL/cl_version.h:34:104: note: #pragma message: cl_version.h: CL_TARGET_OPENCL_VERSION is not defined. Defaulting to 220 (OpenCL 2.2)  
   34 | #pragma message("cl_version.h: CL_TARGET_OPENCL_VERSION is not defined. Defaulting to 220 (OpenCL 2.2)")  
      |                                                                                                        ^  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `boost::compute::device::device(_cl_device_id*, bool)':  
bcholamundo.cpp:(.text._ZN5boost7compute6deviceC2EP13_cl_device_idb[_ZN5boost7compute6deviceC5EP13_cl_device_idb]+0x61): undefined reference to `clRetainDevice'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `boost::compute::device::device(boost::compute::device const&)':  
bcholamundo.cpp:(.text._ZN5boost7compute6deviceC2ERKS1_[_ZN5boost7compute6deviceC5ERKS1_]+0x59): undefined reference to `clRetainDevice'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `boost::compute::device::operator=(boost::compute::device const&)':  
bcholamundo.cpp:(.text._ZN5boost7compute6deviceaSERKS1_[_ZN5boost7compute6deviceaSERKS1_]+0x59): undefined reference to `clReleaseDevice'  
/usr/bin/ld: bcholamundo.cpp:(.text._ZN5boost7compute6deviceaSERKS1_[_ZN5boost7compute6deviceaSERKS1_]+0xa2): undefined reference to `clRetainDevice'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `boost::compute::device::operator=(boost::compute::device&&)':  
bcholamundo.cpp:(.text._ZN5boost7compute6deviceaSEOS1_[_ZN5boost7compute6deviceaSEOS1_]+0x4b): undefined reference to `clReleaseDevice'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `boost::compute::device::~device()':  
bcholamundo.cpp:(.text._ZN5boost7compute6deviceD2Ev[_ZN5boost7compute6deviceD5Ev]+0x47): undefined reference to `clReleaseDevice'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `boost::compute::platform::devices(unsigned long) const':  
bcholamundo.cpp:(.text._ZNK5boost7compute8platform7devicesEm[_ZNK5boost7compute8platform7devicesEm]+0x113): undefined reference to `clGetDeviceIDs'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `boost::compute::platform::device_count(unsigned long) const':  
bcholamundo.cpp:(.text._ZNK5boost7compute8platform12device_countEm[_ZNK5boost7compute8platform12device_countEm]+0x4b): undefined reference to `clGetDeviceIDs'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `boost::compute::system::platforms()':  
bcholamundo.cpp:(.text._ZN5boost7compute6system9platformsEv[_ZN5boost7compute6system9platformsEv]+0x39): undefined reference to `clGetPlatformIDs'  
/usr/bin/ld: bcholamundo.cpp:(.text._ZN5boost7compute6system9platformsEv[_ZN5boost7compute6system9platformsEv]+0xa6): undefined reference to `clGetPlatformIDs'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `unsigned long boost::compute::device::get_info<unsigned long>(unsigned int) const':  
bcholamundo.cpp:(.text._ZNK5boost7compute6device8get_infoImEET_j[_ZNK5boost7compute6device8get_infoImEET_j]+0x23): undefined reference to `clGetDeviceInfo'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > boost::compute::device::get_info<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >(unsigned int) const':  
bcholamundo.cpp:(.text._ZNK5boost7compute6device8get_infoINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEEET_j[_ZNK5boost7compute6device8get_infoINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEEET_j]+0x37): undefined reference to `clGetDeviceInfo'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `_cl_device_id* boost::compute::device::get_info<_cl_device_id*>(unsigned int) const':  
bcholamundo.cpp:(.text._ZNK5boost7compute6device8get_infoIP13_cl_device_idEET_j[_ZNK5boost7compute6device8get_infoIP13_cl_device_idEET_j]+0x23): undefined reference to `clGetDeviceInfo'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `_cl_platform_id* boost::compute::device::get_info<_cl_platform_id*>(unsigned int) const':  
bcholamundo.cpp:(.text._ZNK5boost7compute6device8get_infoIP15_cl_platform_idEET_j[_ZNK5boost7compute6device8get_infoIP15_cl_platform_idEET_j]+0x23): undefined reference to `clGetDeviceInfo'  
/usr/bin/ld: /tmp/ccdYJh1X.o: in function `std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > boost::compute::platform::get_info<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >(unsigned int) const':  
bcholamundo.cpp:(.text._ZNK5boost7compute8platform8get_infoINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEEET_j[_ZNK5boost7compute8platform8get_infoINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEEET_j]+0x37): undefined reference to `clGetPlatformInfo'  
collect2: error: ld returned 1 exit status  
```  
  
Any help would be kindly appreciated.   
  
I am running Ubuntu 20.04, and use opencl 1.2:   
Number of OpenCL platforms: 1  
```  
  
-------------------------  
Platform: NVIDIA CUDA  
Vendor: NVIDIA Corporation  
Version: OpenCL 1.2 CUDA 10.2.185  
Number of devices: 1  
	-------------------------  
		Name: GeForce RTX 2060  
		Version: OpenCL C 1.2   
		Max. Compute Units: 30  
		Local Memory Size: 48.0 KB  
		Global Memory Size: 5931.4375 MB  
		Max Alloc Size: 1482.859375 MB  
		Max Work-group Total Size: 1024  
		Max Work-group Dims:( 1024 1024 64 )  
	-------------------------  
  
-------------------------  
```

---

## Comment 1

> Username: keryell  
> Created at: 2020-08-06 09:59:38 UTC  
> Url: https://github.com/boostorg/compute/issues/850#issuecomment-669835131  

`g++ bcholamundo.cpp` just compiles down to default `a.out` without any specific library. But I guess you need OpenCL somehow...  
`g++ bcholamundo.cpp -lOpenCL` might help.  
  
See for example https://www.boost.org/doc/libs/1_73_0/libs/compute/doc/html/boost_compute/getting_started.html#boost_compute.getting_started.compilation_and_usage

---

## Comment 2

> Username: gavox  
> Created at: 2020-08-06 15:55:42 UTC  
> Url: https://github.com/boostorg/compute/issues/850#issuecomment-670015986  

Perfect, Keryell, thank you so much.
