# #45 modify freelist_test.cpp/spsc_queue_stress_test.cpp/test_common.hpp to adapt VxWorks7 [Merged]

> Username: LiangZhou9527  
> Created at: 2018-11-12 08:54:04 UTC  
> Updated at: 2018-11-12 11:14:32 UTC  
> Merged at: 2018-11-12 10:58:04 UTC  
> Closed at: 2018-11-12 10:58:04 UTC  
> Url: https://github.com/boostorg/lockfree/pull/45  

Why modify freelist_test.cpp and test_common.hpp :  
In VxWorks Kernel, the re-schedule mechanism does not enable "round-robin" reschdule between the same priority tasks by default. So the case "stack_unbounded_stress_test" will lead to test case hang forever. Here's the details:  
  
The test case creates 4 tasks as “Reader”, and then 4 tasks as “Writer”, all those 8 task has the same priority 220.  
If the target has 2 cores, the first 2 reader tasks will occupy the cores by “while (1)” without yield, so the other 2 readers tasks and 4 writers tasks have no chance to be run.  
This will lead to the test case loop forever , then the exp scrip will catch it and print “RTP Execution Timeout; rebooting”  
  
The test case creates 4 tasks as “Reader”, and then 4 tasks as “Writer”, all those 8 task has the same priority 220 in VxWorks Kernel.  
If the target has 2 cores, the first 2 reader tasks will occupy the cores by “while (1)” without yield, so the other 2 readers tasks and 4 writers tasks have no chance to be run.  
This will lead to the test case loop forever without any ending.  
  
Why modify spsc_queue_stress_test.cpp :  
In VxWorks user-land task (Wind River calls it as RTP), the max number of objects (the internal data structure RTP uses) in one RTP is limited at most 65535.  
When the test is selected to run, the C++ constructor will create 1<<16 number of mutex via "new spsc_queue_tester".  
And after test1->run() is finished, the C++ destructor will delete 1<<16 number of mutex by default.  
This caused the problem, because VxWorks just support ((1<<16)-1) objects within an RTP.

---

## Comment 1

> Username: timblechmann  
> Created_at: 2018-11-12 10:57:58 UTC  
> Url: https://github.com/boostorg/lockfree/pull/45#issuecomment-437838716  

thanks a lot!

---

## Comment 2

> Username: tnovotny  
> Created_at: 2018-11-12 11:14:32 UTC  
> Url: https://github.com/boostorg/lockfree/pull/45#issuecomment-437843179  

@timblechmann nice to see that you actually do merge something in once in a while wheras you have been ignoring me for a year and half [#41](https://github.com/boostorg/lockfree/pull/41)

---
