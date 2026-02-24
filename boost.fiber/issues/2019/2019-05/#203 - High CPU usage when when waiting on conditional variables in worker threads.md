# #203 - High CPU usage when when waiting on conditional variables in worker threads [Closed]

> Username: moneroexamples  
> Created at: 2019-05-16 18:45:20 UTC  
> Updated at: 2020-04-03 17:35:57 UTC  
> Closed at: 2019-05-17 06:46:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/203  

In my project I've notice that I have 100% usage per each worker thread when they are waiting at conditional variables. I would expect them to use very little CPU in that case.   
  
This issue can be reproduced using `work_sharing.cpp` and `work_stealing.cpp` examples as well. Removing these sections in the example files:  
  
https://github.com/boostorg/fiber/blob/3f926974cbe06ac2aca312cf9d31abed018b9924/examples/work_sharing.cpp#L52-L56  
  
https://github.com/boostorg/fiber/blob/3f926974cbe06ac2aca312cf9d31abed018b9924/examples/work_stealing.cpp#L52-L56  
  
will cause `fiber_count` not to be decremented. Subsequently, the four working threads will wait on conditional variables for `0 == fiber_count;`  indefinitely. In that case I observer four CPU cores at 100% (four threads).  
  
I think that this has something to do with the fact that the examples use `shared_work` and `work_stealing` schedulers. If I remove the schedulers so that the main and worker threads use default scheduler the issue is not observed.   
  
The issue is observed on `1.69.0-2` on Arch Linux with GCC 8.3. Haven't checked other operating systems nor compiles.

---

## Comment 1

> Username: olk  
> Created at: 2019-05-17 06:46:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/203#issuecomment-493341039  

because the scheduling algorithms are initialized with parameter:  
  
> suspend = false  
  
take a look at the documentation: https://www.boost.org/doc/libs/1_70_0/libs/fiber/doc/html/fiber/scheduling.html#class_work_stealing

---

## Comment 2

> Username: moneroexamples  
> Created at: 2019-05-17 08:12:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/203#issuecomment-493364546  

Thanks. Documentation does not say anything about `suspend` for `shared_work`. Though I see in the source code that it also takes it.

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created at: 2020-04-03 17:35:21 UTC  
> Updated at: 2020-04-03 17:35:57 UTC  
> Url: https://github.com/boostorg/fiber/issues/203#issuecomment-608569141  

Hi,  
  
I did hit the same "issue", while I used `shared_work`, and understood the problem by looking at the code. I also would like to note that the documentation doesn't read anything for `suspend` for the `shared_work` algo.  
  
But I wonder, why would anyone choose `suspend = false` ? Is there really a need to do busy waiting (I find that strange), and if yes, don't you think the default should no busy waiting (because really, I doubt a majority of people want to eat 100% CPU when there is no fiber to schedule) ?  
  
Cheers,  
Romain
