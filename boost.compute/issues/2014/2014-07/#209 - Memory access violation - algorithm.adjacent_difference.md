# #209 - Memory access violation - algorithm.adjacent_difference [Closed]

> Username: kenobrien  
> Created at: 2014-07-31 14:47:34 UTC  
> Updated at: 2014-08-21 06:13:16 UTC  
> Closed at: 2014-08-15 13:02:24 UTC  
> Url: https://github.com/boostorg/compute/issues/209  

Running tests on latest branch with latest Intel OpenCL SDK and runtime on Fedora 20, 64bit. I can add debug info on request.  
  
```  
test 18  
        Start  18: algorithm.adjacent_difference  
  
18: Test command: /home/ken/Projects/compute/test/test_adjacent_difference  
18: Test timeout computed to be: 9.99988e+06  
18: Running 1 test case...  
18: unknown location(0): fatal error in "adjacent_difference_int": memory access violation at address: 0x403fe2580: no mapping at fault address  
18: /home/ken/Projects/compute/test/test_adjacent_difference.cpp(35): last checkpoint  
18:   
18: *** 1 failure detected in test suite "TestAdjacentDifference"  
```  
  
Valgrind output points to OpenCL runtime:  
  
```  
==15683== Thread 1:  
==15683== Invalid read of size 4  
==15683==    at 0x13B491FF: ???  
==15683==    by 0x65A4A0C: ??? (in /opt/intel/opencl-1.2-4.4.0.117/lib64/libOclCpuBackEnd.so)  
==15683==    by 0x5F43ADB: ??? (in /opt/intel/opencl-1.2-4.4.0.117/lib64/libcpu_device.so)  
==15683==    by 0x5A4FA6C: ??? (in /opt/intel/opencl-1.2-4.4.0.117/lib64/libtask_executor.so)  
==15683==    by 0x5A4FC6B: ??? (in /opt/intel/opencl-1.2-4.4.0.117/lib64/libtask_executor.so)  
==15683==    by 0x5CA6B3F: tbb::internal::custom_scheduler<tbb::internal::IntelSchedulerTraits>::local_wait_for_all(tbb::task&, tbb::task*) (custom_scheduler.h:447)  
==15683==    by 0x5CA517F: tbb::internal::generic_scheduler::local_spawn_root_and_wait(tbb::task&, tbb::task*&) (scheduler.cpp:654)  
==15683==    by 0x5CA50AA: tbb::internal::generic_scheduler::spawn_root_and_wait(tbb::task&, tbb::task*&) (scheduler.cpp:662)  
==15683==    by 0x5A4BCE6: ??? (in /opt/intel/opencl-1.2-4.4.0.117/lib64/libtask_executor.so)  
==15683==    by 0x5A4AAC3: ??? (in /opt/intel/opencl-1.2-4.4.0.117/lib64/libtask_executor.so)  
==15683==    by 0x5A49E18: ??? (in /opt/intel/opencl-1.2-4.4.0.117/lib64/libtask_executor.so)  
==15683==    by 0x5A4A2AD: ??? (in /opt/intel/opencl-1.2-4.4.0.117/lib64/libtask_executor.so)  
==15683==  Address 0x40dfcc800 is not stack'd, malloc'd or (recently) free'd  
==15683==   
==15683== Warning: client switching stacks?  SP change: 0x5108a08 --> 0xffefff1a0  
==15683==          to suppress, use: --max-stackframe=68617725848 or greater  
unknown location(0): fatal error in "adjacent_difference_int": memory access violation at address: 0x40dfcc800: no mapping at fault address  
/home/ken/Projects/compute/test/test_adjacent_difference.cpp(35): last checkpoint  
  
*** 1 failure detected in test suite "TestAdjacentDifference"  
  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-01 03:06:58 UTC  
> Url: https://github.com/boostorg/compute/issues/209#issuecomment-50844637  

Interesting. I'll look into this.

---

## Comment 2

> Username: kenobrien  
> Created at: 2014-08-15 13:02:24 UTC  
> Url: https://github.com/boostorg/compute/issues/209#issuecomment-52302909  

Cannot replicate this in most recent release.

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-08-21 06:13:16 UTC  
> Url: https://github.com/boostorg/compute/issues/209#issuecomment-52882199  

Fixed in PR #247.
