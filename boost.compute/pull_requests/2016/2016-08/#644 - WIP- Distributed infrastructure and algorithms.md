# #644 WIP: Distributed infrastructure and algorithms  [Open]

> Username: jszuppe  
> Created at: 2016-08-16 20:39:15 UTC  
> Updated at: 2017-04-08 09:48:43 UTC  
> Url: https://github.com/boostorg/compute/pull/644  

This is a "beta" version of distributed work infrastructure: `distributed::context`, `distributed::command_queue`, `distributed::vector`. I'm open to comments and suggestions.  
  
The idea is pretty simple: `distributed::context` is a wrapper for many OpenCL contexts, `distributed::command_queue` - for many OpenCL command queues, and `distributed::vector` is a container that allocates memory across many devices according to certain weight function.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-08-17 17:58:21 UTC  
> Updated_at: 2016-08-17 18:32:58 UTC  
> Url: https://github.com/boostorg/compute/pull/644#issuecomment-240493917  

I think about removing `blocking` argument from `distributed::vector` ctors and methods and instead use `boost::optional` for an optional reference to a `wait_list` object, but that would add additional dependency. Maybe I'll just remove it for first release and later think about this.  
  
I also think I'll remove the queue from `distributed::vector`. Then I'll also have to change some of its constructors so they don't accept `distributed::context` but `distributed::command_queue` (empty ctor and ctor creating vector with count copies of some value).  
  
It's turned out that having queue in `distributed::vector` may be very problematic... Most methods of `boost::compute::command_queue` are not `const`, so `distributed::vector` would need to have a non-const method that returns reference its distributed queue

---

## Comment 2

> Username: jszuppe  
> Created_at: 2016-08-22 19:30:41 UTC  
> Url: https://github.com/boostorg/compute/pull/644#issuecomment-241523389  

I added `distributed::copy()` algorithm with tests. For now `distributed::copy()` has many different lists of arguments depending on what your copying and in which way. After I introduce `distributed_iterator()`, it will be slightly simpler.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-08-22 19:31:44 UTC  
> Updated_at: 2016-08-22 19:49:47 UTC  
> Url: https://github.com/boostorg/compute/pull/644#issuecomment-241523648  

Also, there are commits which are present in https://github.com/boostorg/compute/pull/648, but I'll remove them before this pull request is ready to be merged. @kylelutz any comments on the code?

---

## Comment 4

> Username: jszuppe  
> Created_at: 2016-08-22 23:27:36 UTC  
> Url: https://github.com/boostorg/compute/pull/644#issuecomment-241582146  

In `copy_async()` I can't use `wait_list` since it does not support event from different contexts. For example `wait()` method does not work properly when wait_list contains events from different contexts, which can happen using `distributed::vector`.  
  
In 2nd half of September I will introduce changes to `wait_list` so it force user to store only `event`s from the same OpenCL context, and maybe make new class for wrapping many events from different contexts (based on `future` or `wait_list`).

---

## Comment 5

> Username: coveralls  
> Created_at: 2016-08-27 22:47:48 UTC  
> Url: https://github.com/boostorg/compute/pull/644#issuecomment-242945128  

[![Coverage Status](https://coveralls.io/builds/7639102/badge)](https://coveralls.io/builds/7639102)  
  
Changes Unknown when pulling **0601a353bf696d0d07d475f687052fd42a8c3538 on haahh:pr_distributed** into *\* on boostorg:develop**.

---

## Comment 6

> Username: coveralls  
> Created_at: 2016-08-27 23:42:47 UTC  
> Url: https://github.com/boostorg/compute/pull/644#issuecomment-242947034  

[![Coverage Status](https://coveralls.io/builds/7639291/badge)](https://coveralls.io/builds/7639291)  
  
Changes Unknown when pulling **0601a353bf696d0d07d475f687052fd42a8c3538 on haahh:pr_distributed** into *\* on boostorg:develop**.

---

## Comment 7

> Username: jszuppe  
> Created_at: 2016-08-28 09:49:44 UTC  
> Url: https://github.com/boostorg/compute/pull/644#issuecomment-242965958  

`distributed::transform()` should have good performance, it's indirectly influenced by the weight function (currently all weights are equal, I will later add weight function based on device performance) since it waits for the slowest device.  
  
`distributed::reduce()` should have good performance when all used devices (queues) are in the same context, but I have an idea how to make it works with the same speed in other cases.

---

## Comment 8

> Username: coveralls  
> Created_at: 2016-08-28 10:41:39 UTC  
> Url: https://github.com/boostorg/compute/pull/644#issuecomment-242967876  

[![Coverage Status](https://coveralls.io/builds/7641271/badge)](https://coveralls.io/builds/7641271)  
  
Changes Unknown when pulling **56fb88137345b4ef499a5467c7505dead6af14e3 on haahh:pr_distributed** into *\* on boostorg:develop**.

---

## Comment 9

> Username: coveralls  
> Created_at: 2016-08-29 21:04:05 UTC  
> Url: https://github.com/boostorg/compute/pull/644#issuecomment-243255649  

[![Coverage Status](https://coveralls.io/builds/7658516/badge)](https://coveralls.io/builds/7658516)  
  
Changes Unknown when pulling **43c31b340180d2eccbee4e7839d60fb0e88a13bf on haahh:pr_distributed** into *\* on boostorg:develop**.

---
