# #811 - Sort algorithms fail running on AMD Radeon RX Vega 56 [Closed]

> Username: rosenrodt  
> Created at: 2019-01-10 11:06:36 UTC  
> Updated at: 2019-04-04 04:26:18 UTC  
> Closed at: 2019-04-04 04:26:18 UTC  
> Url: https://github.com/boostorg/compute/issues/811  

Similar to issue reported in #795, I am facing all kinds of sort() failures with AMD RX Vega GPUs  
  
I ran the test with the command `ctest --output-on-failure`. And here is the summary   
  
### Test result for driver Adrenalin 18.5.2 & 18.9.1  
The following tests FAILED:  
         54 - algorithm.radix_sort (Failed)  
         55 - algorithm.radix_sort_by_key (Failed)  
         75 - algorithm.sort_by_key (Failed)  
         77 - algorithm.stable_sort (Failed)  
        143 - misc.amd_cpp_kernel_language (Failed)  
        148 - example.amd_cpp_kernel (Exit code 0xc0000409)  
  
### Test result for driver Adrenalin 18.12.2     
The following tests FAILED:  
         **41 - algorithm.insertion_sort (Failed)**  
         **45 - algorithm.merge_sort_gpu (Failed)**  
         54 - algorithm.radix_sort (Failed)  
         55 - algorithm.radix_sort_by_key (Failed)  
         **74 - algorithm.sort (Failed)**  
         75 - algorithm.sort_by_key (Failed)  
         77 - algorithm.stable_sort (Failed)  
        143 - misc.amd_cpp_kernel_language (Failed)  
        148 - example.amd_cpp_kernel (Exit code 0xc0000409)  
  
**Bold** items mean the test failed on new driver but not in older drivers  
  
Curiously, with latest drivers it gets even worse.   
  
Note 1. For complete failure reports look [here](https://gist.github.com/rosenrodt/c801be1973d4b41a700a2cd100bc5ef2).     
Note 2. As I recall, AMD Radeon HD 6770 passes every test (maybe except for the amd_cpp_kernel tests)

---

## Comment 1

> Username: rosenrodt  
> Created at: 2019-01-14 15:13:34 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-454038926  

Back with some quick update: Adrenalin 18.12.2 doesn't seem like a quality driver so let's just ignore it. I believe this issue is caused by pointer aliasing inside radix sort kernels. Will post a PR as soon as it is been confirmed

---

## Comment 2

> Username: jszuppe  
> Created at: 2019-01-15 09:05:56 UTC  
> Updated at: 2019-01-15 09:08:30 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-454317055  

Which pointers in the loop are aliasing each other?

---

## Comment 3

> Username: rosenrodt  
> Created at: 2019-01-15 10:44:29 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-454347805  

I figured it’s not pointer aliasing in the scan() kernel I checked (see the work-in-progress pr #812). But rather variable dependency is not detected by the AMD OpenCL compiler.   
  
On the other hand I am seeing really interesting bug with the AMD driver. The compare operators for `char` and `uchar` types are not working so all the errors I get from the tests are emitted by `is_sorted()` when testing on char types. Fortunately the equality check for sorted char arrays are all passed

---

## Comment 4

> Username: jszuppe  
> Created at: 2019-01-18 09:24:21 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-455480712  

Can you check Adrenalin 19.1.1?

---

## Comment 5

> Username: jszuppe  
> Created at: 2019-01-18 09:28:29 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-455481880  

It would also be great to open an issue on AMD.  
  
btw. I guess we should disable/remove AMD C++ tests. They're proboly not supported in new OpenCL drivers.

---

## Comment 6

> Username: rosenrodt  
> Created at: 2019-01-18 10:43:46 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-455504201  

> Can you check Adrenalin 19.1.1?  
  
No luck with 19.1.1 too

---

## Comment 7

> Username: jszuppe  
> Created at: 2019-01-21 11:31:09 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-456042771  

Fixed in #812, @rosenrodt are you planning to open bug for AMD driver?

---

## Comment 8

> Username: rosenrodt  
> Created at: 2019-01-21 12:20:57 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-456055752  

@jszuppe I do but I am not sure where to open a support ticket. Any suggestion?

---

## Comment 9

> Username: jszuppe  
> Created at: 2019-01-21 12:27:55 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-456057564  

I think https://community.amd.com/community/devgurus/opencl is the best place for that. However, they may ask you to make a small, independent program for bug reproduction.

---

## Comment 10

> Username: rosenrodt  
> Created at: 2019-01-21 15:30:11 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-456112218  

I'll post on the forum sometime this week

---

## Comment 11

> Username: rosenrodt  
> Created at: 2019-02-07 02:27:27 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-461266489  

Back with some updates :)   
  
I opened a ticket to report the driver bug that appears on Adrenalin 18.12.2 and onwards https://community.amd.com/message/2897171. The mod can't repro the issue on Hawaii GPUs (no surprise, as it only happens on recent GPUs) so he is passing it to the relevant teams  
  
As for the memory fence workaround I will post as a separate bug report on AMD forum.

---

## Comment 12

> Username: rosenrodt  
> Created at: 2019-03-26 06:15:44 UTC  
> Url: https://github.com/boostorg/compute/issues/811#issuecomment-476489966  

> I opened a ticket to report the driver bug that appears on Adrenalin 18.12.2 and onwards https://community.amd.com/message/2897171. The mod can't repro the issue on Hawaii GPUs (no surprise, as it only happens on recent GPUs) so he is passing it to the relevant teams  
  
Though not directly confirmed by AMD staff yet, the issue of comparing `char` types using `boost::compute::is_sorted()` seems to be resolved as of Adrenalin 19.3.2. Both the standalone minimal test sample and Boost Compute master branch now work as expected.  
  
So I consider this issue resolved.
