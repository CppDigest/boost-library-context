# #844 - Documentation does not mention stack requirements [Open]

> Username: djarek  
> Created at: 2017-10-28 08:37:39 UTC  
> Updated at: 2018-12-21 15:08:49 UTC  
> Url: https://github.com/boostorg/beast/issues/844  

```read``` or ```async_read``` operations may require "large" amounts of stack. The documentation should acknowledge this.  
  
Example: https://github.com/boostorg/beast/blob/ffbe65f4c922e1b8d76e85434d0a8d3ee1c083e9/include/boost/beast/http/impl/basic_parser.ipp#L486  
If the parser is used inside of a stackful coroutine, this may easily lead to stack overflow. A coroutine stack size is architecture dependent and the user may choose a size that is smaller than the default(64k IIRC), but the size should be at least ``` SIGSTKSZ```, which happens to be ```8196``` bytes on linux-amd64.  
  
Additional related thing to consider: with split stacks enabled a large stack allocation might not necessarily improve performance over a dynamically allocated buffer.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-28 12:56:56 UTC  
> Url: https://github.com/boostorg/beast/issues/844#issuecomment-340189047  

Are you suggesting any implementation changes?

---

## Comment 2

> Username: djarek  
> Created at: 2017-10-28 15:51:28 UTC  
> Url: https://github.com/boostorg/beast/issues/844#issuecomment-340200574  

From the point of view of the use cases I've had for Beast, it would make sense if the parser were either:  
- more configurable   
- had variants (```fast_parser```, ```lean_parser```)  
- easy to replace with your own parser (using "parser building blocks" from Beast)  
  
At this time I'm not sure which approach is best.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-28 15:55:00 UTC  
> Url: https://github.com/boostorg/beast/issues/844#issuecomment-340200839  

What kinds of configuration options?

---

## Comment 4

> Username: djarek  
> Created at: 2017-10-28 16:07:43 UTC  
> Updated at: 2017-10-28 16:08:31 UTC  
> Url: https://github.com/boostorg/beast/issues/844#issuecomment-340201689  

Definitely:  
- memory allocation strategy (e.g. try to use stack, heap or maybe an arena allocator).  
  
Would be nice:  
- supported features - in my case I only ever needed POST, GET, PUT, DELETE. and a only handful of status codes. All the other things were defined as errors by the REST API spec. For this one, it would probably make more sense to enable easy implementation of your own parser.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-28 16:21:17 UTC  
> Url: https://github.com/boostorg/beast/issues/844#issuecomment-340202524  

* `basic_parser` is not supposed to allocate memory at all, but I see your point about the stack usage  
  
* If you want to support only a subset of verbs you can subclass `basic_parser` yourself, and raise an error for verbs you don't want.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-10-28 16:58:55 UTC  
> Url: https://github.com/boostorg/beast/issues/844#issuecomment-340204990  

I see now, you want to reduce the footprint of Beast for embedded devices, see #845
