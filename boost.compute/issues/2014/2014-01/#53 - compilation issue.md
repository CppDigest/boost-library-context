# #53 - compilation issue [Closed]

> Username: sajis997  
> Created at: 2014-01-20 06:28:48 UTC  
> Updated at: 2014-01-21 02:37:10 UTC  
> Closed at: 2014-01-21 02:37:10 UTC  
> Url: https://github.com/boostorg/compute/issues/53  

Having the following compilation error:  
  
///////////////////////////////////////////  
  
g++ -O2 -g -o main.o -c main.cpp -I/usr/local/cuda-5.5/include -I/home/sajjad/Downloads/OpenCL/library/clMath/clFFT/clFFT-develop/src/include -I/home/sajjad/Downloads/OpenCL/library/BoostCompute/compute-master/include   
In file included from /home/sajjad/Downloads/OpenCL/library/BoostCompute/compute-master/include/boost/compute/system.hpp:21:0,  
                 from /home/sajjad/Downloads/OpenCL/library/BoostCompute/compute-master/include/boost/compute/algorithm/accumulate.hpp:16,  
                 from /home/sajjad/Downloads/OpenCL/library/BoostCompute/compute-master/include/boost/compute/algorithm.hpp:18,  
                 from /home/sajjad/Downloads/OpenCL/library/BoostCompute/compute-master/include/boost/compute.hpp:14,  
                 from main.cpp:3:  
/home/sajjad/Downloads/OpenCL/library/BoostCompute/compute-master/include/boost/compute/device.hpp:17:31: fatal error: boost/move/move.hpp: No such file or directory  
compilation terminated.  
make: **\* [main.o] Error 1  
  
///////////////////////////////////////////////////////  
  
I checked the directory structure and neither the directory nor the file exists.  
  
Any hint to get around this issue?  
  
Thanks

---

## Comment 1

> Username: ddemidov  
> Created at: 2014-01-20 06:49:46 UTC  
> Url: https://github.com/boostorg/compute/issues/53#issuecomment-32737404  

Boost.Move was introduced in Boost v1.48. What Boost version do you have installed?

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-01-21 02:36:58 UTC  
> Url: https://github.com/boostorg/compute/issues/53#issuecomment-32816430  

Like Denis said, Boost.Compute requires Boost version 1.48 or later. I've added a version check to the Bost.Compute headers (see 47922aa780ae2aa493ad0b22a968c1e05e602777) which should now print out a message like the following if the installed Boost version is too old:  
  
```  
/home/kyle/dev/compute/include/boost/compute/config.hpp:20:2: error: #error Boost.Compute requires Boost version 1.48 or later  
 #error Boost.Compute requires Boost version 1.48 or later  
  ^  
```
