# #303 - Full async interface [Closed]

> Username: bkchr  
> Created at: 2014-11-08 01:35:39 UTC  
> Updated at: 2017-10-01 14:57:33 UTC  
> Closed at: 2017-10-01 14:57:33 UTC  
> Url: https://github.com/boostorg/compute/issues/303  

Hi,   
I'm working for a project where we need full async communication with OpenCL, but we also want to use some of your basic functions like "transform". So I created a first implementation for a "transform_async", that's not so complicated at all, but to get real async operations working some internal changes are required as well. Buffer and kernel objects now can save events, these events are passed to OpenCL to ensure that an operation on a buffer is executed after the previous operation is finished.   
What do you think in overall about this idea? Do you maybe would implement some parts different?   
I hope that we can find a way, that this features finds a way into your repo.  
  
Here is a link to my repo: https://github.com/bastiankoe/compute  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-11-08 03:01:27 UTC  
> Updated at: 2014-11-08 05:36:10 UTC  
> Url: https://github.com/boostorg/compute/issues/303#issuecomment-62243063  

All calls to `enqueue_nd_range_kernel()` are already asynchronous. So any algorithms which just create and execute kernels on the device (like `transform()`) are also asynchronous (with respect to code executing on the host).  
  
If you need to access to an event for tracking the status of a kernel/algorithm you can use the `command_queue::enqueue_barrier()` function like so:  
  
```  
// enqueue transform kernel  
boost::compute::transform(v.begin(), v.end(), op, queue);  
  
// get event which will complete after transform kernel is finished  
boost::compute::event e = queue.enqueue_barrier();  
  
// do stuff on host  
std::cout << "running host code" << std::endl;  
  
// wait for transform to complete  
e.wait();  
```  
  
And if you want to use the `future<>` interface you can create one from an event like this:  
  
```  
auto future = boost::compute::future<void>(queue.enqueue_barrier());  
```  
  
The nice thing about this approach is you can queue up the execution many different algorithms and/or custom kernels and then get an event which will complete when all of them have completed (or at any point in between).  
  
Let me know if this will work for your use-case. I'll look more into the patches you have. Thanks!

---

## Comment 2

> Username: bkchr  
> Created at: 2014-11-08 13:12:07 UTC  
> Url: https://github.com/boostorg/compute/issues/303#issuecomment-62257235  

Okay, that's a nice feature. I didn't know about this. But we need something like(simplified ;D):  
boost::compute::copy_async(myvector);  
boost::compute::transform_async(myvector);  
  
We would like to create a chain on the host and OpenCL is responsible for waiting until the copy operation is finished and would activate the transform operation. Do you understand what are my intentions?

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-11-08 18:26:56 UTC  
> Updated at: 2014-11-08 18:35:27 UTC  
> Url: https://github.com/boostorg/compute/issues/303#issuecomment-62269842  

I do. That is the execution model that OpenCL already provides. Each time you call a Boost.Compute algorithm it just queues up the necessary operations on the provided command queue. The operations (e.g. copies or kernel launches) will then be executed sequentially by the compute device (and asynchronously with respect to code running on the host).  
  
The only complication is that some algorithms must necessarily synchronize with the host. For example, the `accumulate()` algorithm returns a value which cause the function to block until the computation is complete and the result is copied from device memory to host memory. However, you can avoid this by running algorithms which only perform operations on the device (like `reduce()` with the result buffer in device memory) and then use `copy_async()` to asynchronously transfer the result to the host.  
  
Let me know if this doesn't work as intended. If you could provide some example code that would be great. Hope this helps!

---

## Comment 4

> Username: bkchr  
> Created at: 2014-11-10 13:05:00 UTC  
> Url: https://github.com/boostorg/compute/issues/303#issuecomment-62381315  

Yeah, you're right if you are using the standard command queue, but if you are setting the property "CL_QUEUE_OUT_OF_ORDER_EXEC_MODE_ENABLE" the execution of OpenCL commands on the device isn't sequential anymore. In this case you need the events to ensure that you're kernel is waiting  for the copy operation.   
So each call to OpenCL functions needs the list of events to wait for. I've done some internal api, so  this wait_list is build implicit before calling a kernel or a copy operation.   
Another use case is the use of two different command queues. Some architectures require two command queues, one for transfer and one for execution. To synchronize between these two command queues you also need the events.  
  
That an algorithm has an asynchronous mode could easily signaled via "_async" in the name.

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-11-11 05:23:46 UTC  
> Url: https://github.com/boostorg/compute/issues/303#issuecomment-62503675  

Yes, that is true. For out-of-order command queues and/or synchronizing between two separate command queues you would need to explicitly state the dependencies with events.  
  
Truthfully I have thought about this for a while but never came up with an API that I liked (and the current API we have handles a large number of use-cases). The problem I see with the approach you've suggested is that each algorithm would now have multiple different "execution modes" which leads to a combinatorial explosion in the number of functions which would have to be provided in the public API. This can be burdensome both for users of the library and also for maintainers of the library.  
  
In the past I've played around with a potential API like this:  
  
```  
// split the values and sort each half on a separate device  
future<void> sort1_future = async(sort(), v.begin(), v.begin() + v.size() / 2, queue);  
future<void> sort2_future = async(sort(), v.begin() + v.size() / 2, v.end(), other_queue);  
  
// merge values once both sorts are finished  
future<void> merge_future = async_after(  
    inplace_merge(),  
    v.begin(),  
    v.begin() + v.size() / 2,  
    v.end(),  
    { sort1_future, sort2_future },  
    queue  
);  
  
// do some work on the host  
std::cout << "host code" << std::endl;  
  
// block until the merge is complete and then transfer the results back to the host  
sync_after(copy(), v.begin(), v.end(), host.begin(), { merge_future }, queue);  
```  
  
Basically the function execution wrapper takes the algorithm, the arguments, a wait list (for the `_after` variants), and a command queue and returns the result (or `future<>` result for the `async_` variants).  
  
By just adding a few new execution function wrappers (`async()`, `async_after()`, `sync_after()`) we could augment the current algorithm API (which already has over 70 different algorithms) with async functionality without duplicating a huge amount of code. Basically this changes the overall number of functions from `N*M` (`N` modes like `sync`, `async`, `async_after` times `M`, the total number of algorithms) to just `N+M`. This keeps the API smaller and easier to understand (and also easier to maintain). This style of API could also be made more extensible and allow for arbitrary user functions/algorithms to be made asynchronous and work within the framework.  
  
For reference, see this paper: http://www.cs.berkeley.edu/~kamil/papers/ipdps14.pdf  
  
Also, if you haven't seen it, read through this thread (http://boost.2283326.n4.nabble.com/compute-GPGPU-Library-Request-For-Feedback-td4643691.html) which I made on the Boost mailing list a while back about Boost.Compute. It has some discussion of asynchronous APIs and how they could evolve.  
  
This is a fairly hard problem, both for the design and implementation. Thanks for bringing this up and for working on it! I'd be very interested to hear any ideas you have!

---

## Comment 6

> Username: kylelutz  
> Created at: 2014-11-20 06:11:28 UTC  
> Url: https://github.com/boostorg/compute/issues/303#issuecomment-63765587  

@bastiankoe These are great ideas and I'd very much like to discuss them further with you. Could you post a message with your thoughts on the mailing-list (https://groups.google.com/forum/#!forum/boost-compute) so we can start a larger discussion about your use-case and how to best support it?

---

## Comment 7

> Username: bkchr  
> Created at: 2014-11-28 12:03:11 UTC  
> Url: https://github.com/boostorg/compute/issues/303#issuecomment-64887097  

Sorry for my late reply. In the next days I will post something at the mailinglist so that we can discuss this further.

---

## Comment 8

> Username: Ulfgard  
> Created at: 2015-11-18 11:54:25 UTC  
> Url: https://github.com/boostorg/compute/issues/303#issuecomment-157689055  

I would like to rise awareness to this issue again as I also stumbled upon it recently. Here a simplified use case in an out-of-order queue:  
  
```  
using namespace boost::compute;  
vector<float> a,b;  
event e = copy_async(b.begin(),b.end(),a.begin()).get_event();  
fill(b.begin(),b.end(),0);//bad without synchronization  
```  
  
I would propose a simple interface where the wait_list is appended as extra parameter to all functions (snychronous and asynchronous):  
  
```  
fill(b.begin(),b.end(),0,system::default_queue(),wait_list(e));  
```

---

## Comment 9

> Username: kylelutz  
> Created at: 2015-11-20 04:01:03 UTC  
> Url: https://github.com/boostorg/compute/issues/303#issuecomment-158273328  

@Ulfgard Yeah, currently the Boost.Compute algorithms do not fully support out-of-order command queues.  
  
Adding an additional `wait_list` parameter to all of the current functions would be a large undertaking, and instead I think it would be more useful to support this in a potential "version 2" of the algorithms API (potentially with an executors-style interface). I'd be interested to hear your thoughts on this.

---

## Comment 10

> Username: Ulfgard  
> Created at: 2015-11-20 06:28:42 UTC  
> Updated at: 2015-11-20 06:36:17 UTC  
> Url: https://github.com/boostorg/compute/issues/303#issuecomment-158294480  

@kylelutz I can understand that it is a large undertaking. I myself decided to copy your implementation of copy_on_device, added the wait_list parameter to it and exec_1d in copy_kernel and used this for my library. The only problem is that as soon as you change some of the internals, this will break, but I guess i have to live with that for now.  
  
I am not sure how you would redesign the API. One could see the command_queue already as an executor that handles task scheduling, the only thing that has to be done from user side is adding the dependencies to the tasks that are scheduled. Note that the interface design in the current discussion of the standard does not allow for explicit handling of dependencies but only for indirect handling through futures and then() statements. I am not sure about the current state of the discussion but i think at some point scheduling a task after some other task was handled similar to  
  
```  
when_all(future1,future2,...futureN).then(executor,myTaskFunction);  
```  
  
But this is not straight forward to implement on GPUs. In simple threading we can wait to add myTaskFunction to the executor until all dependencies are ready, but on GPUs we have the latency of the command queue which makes this approach inefficient. Further, I am unsure how i would transform a call to std::copy or std::fill to something that fits this interface. The standard library can encode this via a lambda function replacing myTaskFunction. On GPUs we do not have such syntax sugar...  
  
//edit:  
Personally i could understand if you do not change the high level interface as this is a complicated task. But in that case it would be nice to be able to circumvent the algorithm api and directly schedule tasks on the command_queue. However this requires the kernels to be available in a non-detail namespace with an interface compatible to the command_queue.
