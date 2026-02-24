# #197 - Test failure - intersection [Open]

> Username: kenobrien  
> Created at: 2014-07-18 17:02:35 UTC  
> Updated at: 2017-03-21 04:14:55 UTC  
> Url: https://github.com/boostorg/compute/issues/197  

The entire test suit succeeds except for this one. How should I proceed?  
  
```  
The following tests FAILED:  
     64 - algorithm.set_intersection (Failed)  
Errors while running CTest  
  
```  
  
Can confirm that "iter" doesn't seem to be updating. I printed it, it's zero.  
  
```  
/home/ken/Projects/compute/test/test_set_intersection.cpp:41: void compute_test::set_intersection_int::test_method(): Assertion `iter == result.begin()+5' failed.  
unknown location(0): fatal error in "set_intersection_int": signal: SIGABRT (application abort requested)  
```  
  
Running on 64bit OpenSuse 13.1, with Nvidia device as only OpenCL device:  
  
```  
CUDA Device Query (Runtime API) version (CUDART static linking)  
  
Detected 1 CUDA Capable device(s)  
  
Device 0: "GeForce GTX 470"  
  CUDA Driver Version / Runtime Version          6.0 / 6.0  
  CUDA Capability Major/Minor version number:    2.0  
  Total amount of global memory:                 1280 MBytes (1341718528 bytes)  
  (14) Multiprocessors, ( 32) CUDA Cores/MP:     448 CUDA Cores  
  GPU Clock rate:                                1215 MHz (1.22 GHz)  
  Memory Clock rate:                             1674 Mhz  
  Memory Bus Width:                              320-bit  
  L2 Cache Size:                                 655360 bytes  
  Maximum Texture Dimension Size (x,y,z)         1D=(65536), 2D=(65536, 65535), 3D=(2048, 2048, 2048)  
  Maximum Layered 1D Texture Size, (num) layers  1D=(16384), 2048 layers  
  Maximum Layered 2D Texture Size, (num) layers  2D=(16384, 16384), 2048 layers  
  Total amount of constant memory:               65536 bytes  
  Total amount of shared memory per block:       49152 bytes  
  Total number of registers available per block: 32768  
  Warp size:                                     32  
  Maximum number of threads per multiprocessor:  1536  
  Maximum number of threads per block:           1024  
  Max dimension size of a thread block (x,y,z): (1024, 1024, 64)  
  Max dimension size of a grid size    (x,y,z): (65535, 65535, 65535)  
  Maximum memory pitch:                          2147483647 bytes  
  Texture alignment:                             512 bytes  
  Concurrent copy and kernel execution:          Yes with 1 copy engine(s)  
  Run time limit on kernels:                     Yes  
  Integrated GPU sharing Host Memory:            No  
  Support host page-locked memory mapping:       Yes  
  Alignment requirement for Surfaces:            Yes  
  Device has ECC support:                        Disabled  
  Device supports Unified Addressing (UVA):      Yes  
  Device PCI Bus ID / PCI location ID:           3 / 0  
  Compute Mode:  
     < Default (multiple host threads can use ::cudaSetDevice() with device simultaneously) >  
  
deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 6.0, CUDA Runtime Version = 6.0, NumDevs = 1, Device0 = GeForce GTX 470  
Result = PASS  
  
```  
##

---

## Comment 1

> Username: kenobrien  
> Created at: 2014-07-18 18:45:04 UTC  
> Updated at: 2014-07-18 18:54:35 UTC  
> Url: https://github.com/boostorg/compute/issues/197#issuecomment-49465913  

Ok, I've debugged this.  
  
```  
BOOST_AUTO_TEST_CASE(set_intersection_int)  
{  
    int dataset1[] = {1, 1, 2, 2, 2, 2, 3, 3, 4, 5, 6, 10};  
    bc::vector<bc::int_> set1(dataset1, dataset1 + 12, queue);  
  
    int dataset2[] = {0, 2, 2, 4, 5, 6, 8, 8, 9, 9, 9, 13};  
    bc::vector<bc::int_> set2(dataset2, dataset2 + 12, queue);  
  
    bc::vector<bc::uint_>result(10, queue.get_context());  
  
    bc::vector<bc::uint_>::iterator iter =  
        bc::set_intersection(set1.begin(), set1.begin() + 12,   
                             set2.begin(), set2.begin() + 12,   
                             result.begin(), queue);  
  
    CHECK_RANGE_EQUAL(int, 5, result, (2, 2, 4, 5, 6));  
    BOOST_VERIFY(iter == result.begin()+5);  
}  
```  
  
The set_intersection() returns the end of the iterator which goes to the end of the result vector.    Iter is only every equal to result.begin+N, when N is equal to the result's size, which is why the character tests passes.   
  
Obviously changing the size of the result vector passes the test, but the underlying problem appears to be in set_intersection.hpp. I'll prepare a patch.

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-07-18 20:43:41 UTC  
> Url: https://github.com/boostorg/compute/issues/197#issuecomment-49477534  

@roshanr95 thoughts on this?

---

## Comment 3

> Username: roshanrags  
> Created at: 2014-07-19 06:52:14 UTC  
> Url: https://github.com/boostorg/compute/issues/197#issuecomment-49501636  

The algorithm is designed to return the end of the actual result(the intersection), not the end of the `result` vector which it is doing by counting the number of elements in the intersection and adding it to `result`'s beginning. Also, nowhere in the algorithm is the _size_ of `result` vector used. So, I fail to see how changing the size will make any difference.

---

## Comment 4

> Username: roshanrags  
> Created at: 2014-07-19 06:59:06 UTC  
> Url: https://github.com/boostorg/compute/issues/197#issuecomment-49501750  

Moreover, all the set algorithms have the same code except for the serial kernels. So, if only `set_intersection` is failing, it most probably is something with its serial kernel. I'll check it out, but I doubt I can do much since I am not able to reproduce this.  
  
@kenobrien Some debugging output from your side would be helpful. Printing out the contents of `temp_a`, `temp_b` and `counts` after execution of each of the three kernels used in the algorithm would be a good starting point to check if the individual kernels are working as expected.

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-07-24 02:31:04 UTC  
> Url: https://github.com/boostorg/compute/issues/197#issuecomment-49960907  

@kenobrien I'm also having trouble reproducing this. Can you try adding some print statements to the `set_intersection()` function and post the results? You can use the `print_range()` utility function to print a range of values like this:  
  
```  
std::cout << "counts = ";  
detail::print_range(counts.begin(), counts.end(), queue);  
```  
  
Hopefully we can get this fixed.

---

## Comment 6

> Username: f-koehler  
> Created at: 2014-07-29 06:53:06 UTC  
> Updated at: 2014-08-07 06:12:47 UTC  
> Url: https://github.com/boostorg/compute/issues/197#issuecomment-50441729  

I need to take a look at it as I can reproduce this failure.  
  
I am running Archlinux with the latest AMD APP SDK installed.

---

## Comment 7

> Username: kenobrien  
> Created at: 2014-07-31 14:43:17 UTC  
> Url: https://github.com/boostorg/compute/issues/197#issuecomment-50768189  

I will add more feedback soon. I reinstalled my machine recently.

---

## Comment 8

> Username: kenobrien  
> Created at: 2014-08-15 13:40:20 UTC  
> Url: https://github.com/boostorg/compute/issues/197#issuecomment-52306346  

@kylelutz   
  
I've added this print functions to set_intersection.hpp just before it returns "return result + detail::read_single_value<uint_>(counts.get_buffer(), counts.size()-1, queue);"  
  
```  
[ken@mjolnir compute]$ ./test/test_set_intersection   
Running 2 test cases...  
counts = [ 0, 1, 2, 3, 5, 5, 5 ]  
test_set_intersection: /home/ken/Projects/compute/test/test_set_intersection.cpp:39: void compute_test::set_intersection_int::test_method(): Assertion `iter == result.begin()+5' failed.  
unknown location(0): fatal error in "set_intersection_int": signal: SIGABRT (application abort requested)  
counts = [ 0, 2, 3, 4, 5, 5 ]  
  
*** 1 failure detected in test suite "TestSetIntersection"  
  
```
