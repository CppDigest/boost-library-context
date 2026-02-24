# #719 - Should enqueue_write_buffer() etc. be fully blocking/sync? [Open]

> Username: jszuppe  
> Created at: 2017-05-09 18:54:16 UTC  
> Updated at: 2017-05-16 13:45:24 UTC  
> Url: https://github.com/boostorg/compute/issues/719  

If you look at [enqueue_write_buffer()](https://github.com/boostorg/compute/blob/master/include/boost/compute/command_queue.hpp#L432) implementation (and other writes methods without `_async` suffix), you can think that all those operations are 100% blocking, i.e., they do not return until memory pointed to by `host_ptr` has been copied into the buffer. However, that's not true.  
  
In the OpenCL specification [`clEnqueueWriteBuffer`](https://www.khronos.org/registry/OpenCL/sdk/1.2/docs/man/xhtml/clEnqueueWriteBuffer.html) only promises: If blocking_write is `CL_TRUE`, the OpenCL implementation copies the data referred to by `ptr` and **enqueues the write operation** in the command-queue. The memory pointed to by `ptr` can be reused by the application after the clEnqueueWriteBuffer call returns.  
  
In other words, it is blocking is a sense that it can return only after it has made sure that the memory pointed by `ptr` can be reused by the application. It can just internally copy the memory pointed by `ptr` to some temporary host buffer and return immediately after that. That's what Intel's OpenCL platform does for CPU devices (and I don't know any other implementation that behaves that way).   
  
Usually it's not a problem, because user typically has one in-order command queue. However, if you write to and read from buffer using different command queues, you have a race condition. In Boost.Compute it might happen quite often, because for `operator[]`s and in iterators we construct temporary command queues based on context of underlying buffer.   
  
In commit https://github.com/boostorg/compute/commit/0f5c49d5f5f12d9a8b32e2193465f2279fb21b16 I fixed that problem for writing using `operator[]` and iterators, which fixes situations like:  
  
```cpp  
bc::vector<int> vector(10, context);  
vector[0] = 3;  
if(vector[0] == 3) // 100% sure it will be 3 at the time of read  
{  
...  
}  
```  
, but code like this:  
  
```cpp  
bc::vector<int> vector(context);  
vector.push_back(3, queue);  
vector.at(0); // does not have to be 3 at the time of read  
```  
still has a race condition (in some OpenCL implementations, currently I know this happens only on Intel).  
  
The question is if we should make `enqueue_write_buffer()` and other write methods in `command_queue` fully, 100% blocking, which would require waiting on an event returned by write calls like `clEnqueueWriteBuffer`.

---

## Comment 1

> Username: Ulfgard  
> Created at: 2017-05-13 12:21:28 UTC  
> Updated at: 2017-05-13 13:09:39 UTC  
> Url: https://github.com/boostorg/compute/issues/719#issuecomment-301244816  

Hi,  
  
I stumbled across this exact problem in my own code when creating an array of size one and read/write to the element with operator[]. For this exact case, there is already another issue here #643 .  
//edit also relevant: #707 and #690.  
  
From the OpenCL point of view, reading or writing to the same object from different queues is fundamentally broken, because the runtime does not, and can not, put any guarantees here. The issue is not that clEnqueueWriteBuffer does not fully complete, this is fine according to the runtime model and will never fail as long as there are not multiple queues modifying the same objects. The expectation of the user should be that he can free the memory on his end, nothing more.  
  
Also, to guarantee that this works, ALL reads and writes must be fully blocking because of the race conditions you mentioned. And this means fundamentally: All operations have to block, because in the case of:  
  
```  
algorithm1(container.begin(),container.end(),queue);  
container[0]=val;  
algorithm2(container.begin(),container.end(),queue);  
val=container[0];  
```  
  
The only thing that is guaranteed is that algorithm2 is executed after algorithm1 (in an in-order-queue, of course). And i think it is also not guaranteed that the write to the container can not happen while the kernel of algorithm1 is running(in case it starts before the write). I have no idea how to do any of this when feeding the queue from multiple threads.  
  
Note that the situation becomes even more severe in out-of-order queues where the queue is allowed to do reordering of operations which might also lead to an reordering of memory transfers. Here forcing the blocking to be a full block could essentially kill any advantage of out-of-order computations.  
  
From my perspective the issue lies with the containers not being assigned to a queue. One might argue that buffers in OpenCL are not bound to a queue, so why should containers be; however, no operation can be performed without a queue, so why should container be allowed to do this, or even worse: construct their own?   
  
So the solution should be that containers behave like:  
1. not giving a queue results in the container using the default queue of the device. It should never result in using an _arbitrary_ newly constructed queue. This will be okay for the majority of cases where operations only happen on a single queue.  
2. changing the queue should result in all operations being done on the queue

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-05-13 14:02:29 UTC  
> Url: https://github.com/boostorg/compute/issues/719#issuecomment-301250336  

>The issue is not that clEnqueueWriteBuffer does not fully complete, this is fine according to the runtime model and will never fail as long as there are not multiple queues modifying the same objects.  
  
There is an issue. The description of blocking write in `clEnqueueWriteBuffer` is clear, however, unfortunately, other parts of the OpenCL API specification describe different behaviour for blocking operations. For example:  
  
>For blocking commands, the OpenCL API functions that enqueue commands don’t return until the command has completed.   
  
>Blocking Commands: A blocking command defines a synchronization point between the unit of execution that calls the blocking API function and the enqueued command reaching the complete state.  
  
That's why there was [is?] a confusion, hence my question.  
  
>From the OpenCL point of view, reading or writing to the same object from different queues is fundamentally broken, because the runtime does not, and can not, put any guarantees here.   
  
It would not say it's broken. The synchronisation in such situations (i.e. when using async operations) is a user's responsibility. I don't know how it would effect performance if you would like OpenCL platform to do all this. I would not like to add any new guarantees into Boost.Compute, except those that are in OpenCL.  
  
> From my perspective the issue lies with the containers not being assigned to a queue.   
  
Storing a queue in containers is an idea worth consideration. I agree that creating a lot of command queues with very short lifetime is not the best solution. I was thinking about this [storing queue in `bc::vector`] for some time, but there are some problem and I didn't have time to find good solution for them. for example: there is no such thing as "a default queue of the device", so what should we do when user uses ctor with context (`!= system::default_context()`)?

---

## Comment 3

> Username: Ulfgard  
> Created at: 2017-05-16 07:38:21 UTC  
> Url: https://github.com/boostorg/compute/issues/719#issuecomment-301701125  

> for example: there is no such thing as "a default queue of the device", so what should we do when user uses ctor with context (!= system::default_context())?  
  
I would introduce a breaking change that vectors are created from a queue instead of a context. So either it is the default queue=default context or the user has to provide the queue (and thus the context).

---

## Comment 4

> Username: jszuppe  
> Created at: 2017-05-16 12:37:12 UTC  
> Url: https://github.com/boostorg/compute/issues/719#issuecomment-301768700  

>I would introduce a breaking change that vectors are created from a queue instead of a context. So either it is the default queue=default context or the user has to provide the queue (and thus the context).  
  
I understand, so no ctor from context. I guess it would simplify many things. However, you realise that it does not help with many command queues problem (when user use async operations). Code like:  
  
```cpp  
// container create with queue1  
algorithm(container.begin(), container.end(), queue2); // async  
container[0] = val; // queue1  
```  
  
would still have a race condition.

---

## Comment 5

> Username: Ulfgard  
> Created at: 2017-05-16 13:45:11 UTC  
> Updated at: 2017-05-16 13:45:24 UTC  
> Url: https://github.com/boostorg/compute/issues/719#issuecomment-301786810  

Yes, this is true. But I consider this case as very rare. Having multiple queues in the same context is already a quite academic use-case and then having several objects shared between queues is even more unlikely. This is because we have Out-of-Order queues which are designed for this task.   
  
We could just add a small helper like:  
```  
algorithm(container.begin(), container.end(), queue2); // async  
on_queue(container,queue2)[0] = val; // executed on queue2  
```  
  
I find it more dangerous from the point of view of Out-Of-Order queues, that we do not get an event from the assignment.
