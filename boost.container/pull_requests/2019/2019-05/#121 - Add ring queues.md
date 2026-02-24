# #121 Add ring queues [Closed]

> Username: Lastique  
> Created at: 2019-05-19 23:06:53 UTC  
> Updated at: 2020-07-12 10:49:49 UTC  
> Closed at: 2020-07-12 10:49:48 UTC  
> Url: https://github.com/boostorg/container/pull/121  

This PR proposes two new components for inclusion in Boost.Container:  
  
- `ring_queue` - a FIFO queue, similar to `std::queue`, which uses a circular buffer to store elements, and  
- `small_ring_queue` - same as `ring_queue` but with a statically allocated internal storage for a (small) number of elements.  
  
The queues are more optimal than `std::queue` in cases when maximum number of enqueued elements is relatively constant. Circular buffer allows to effectively eliminate dynamic memory allocations, making queue performance more stable. `small_ring_queue` allows to eliminate memory allocations completely, provided that the queue does not exceed the internal capacity. Given that the circular buffer is contiguous, the queues are also more CPU cache friendly.  
  
This PR adds implementation, tests and documentation. The implementation requires C++17 currently, for this reason I did not include forward declarations in `container_fwd.hpp`.

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-06-23 17:19:42 UTC  
> Url: https://github.com/boostorg/container/pull/121#issuecomment-504770797  

Ping, any feedback?

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2019-06-23 20:39:03 UTC  
> Url: https://github.com/boostorg/container/pull/121#issuecomment-504785056  

Thanks for the proprosal.  
  
I haven't received any interest from users on these type of containers. How is different from a circular buffer? Maybe you should propose them for Boost circular buffer library, or start a discussion in the mailing list (maybe there was one but I missed it).  
  
Note: Being C++17-only is also a problem for Boost.Container, it still supports C++03 for all elements.

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-06-24 01:21:23 UTC  
> Url: https://github.com/boostorg/container/pull/121#issuecomment-504807427  

`circular_buffer` has a different design, which is not quite compatible with the ring queue I implemented. In particular, `circular_buffer` usage pattern implies that the user sets the buffer capacity and then proceeds pushing elements to the container; the container then transparently replaces the oldest elements as it "rolls over". Ring queue does not overwrite enqueued elements, you have to dequeue them, as you normally would with `std::queue`.  
  
Regarding C++17 requirement, I could probably relax it, although it would complicate code quite a bit. I thought there were C++11-only components in Boost.Container, but I might be mistaken. If there is interest, I can try lowering it to C++11; for C++03 I think it would require quite a lot of effort, while the benefit isn't clear.

---

## Comment 4

> Username: SteelBlueVision  
> Created_at: 2020-05-21 21:29:10 UTC  
> Url: https://github.com/boostorg/container/pull/121#issuecomment-632356152  

This actually looks quite useful. Is it still being considered for inclusion?

---

## Comment 5

> Username: igaztanaga  
> Created_at: 2020-07-12 10:49:48 UTC  
> Url: https://github.com/boostorg/container/pull/121#issuecomment-657205430  

The proposal was discussed in the mailing list, and Boost.CircularBuffer or an independent library seems a better way to introduce them in Boost.

---
