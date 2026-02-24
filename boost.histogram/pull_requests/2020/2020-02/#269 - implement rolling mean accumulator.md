# #269 implement rolling mean accumulator [Closed]

> Username: jbuonagurio  
> Created at: 2020-02-29 04:58:00 UTC  
> Updated at: 2020-03-07 13:42:59 UTC  
> Closed at: 2020-03-07 13:42:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/269  

As requested in #250, this PR includes a basic rolling mean accumulator for trivial value types and a basic circular buffer implementation to eliminate the dependency on `boost::circular_buffer`.  
  
Work-in-progress while awaiting support for arguments in `storage_adaptor`. Will of course help with unit tests as well!

---

## Comment 1

> Username: jbuonagurio  
> Created_at: 2020-02-29 07:41:02 UTC  
> Url: https://github.com/boostorg/histogram/pull/269#issuecomment-592915566  

I added the simple forward iterator to `circular_buffer` just for debugging purposes.

---

## Review 2 [Commented]

> Username: HDembinski  
> Created_at: 2020-03-06 09:07:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/269#pullrequestreview-369411253  

There is also an exponential moving average, https://en.wikipedia.org/wiki/Moving_average#Exponential_moving_average, which can be computed without adding extra storage. This could be interesting to add as well.  
  
The cumulative moving average can also be computed without extra storage.  
https://en.wikipedia.org/wiki/Moving_average#Cumulative_moving_average  
  
Finally, we have several rolling mean implementations in Boost.Accumulators, which can be used with Boost.Histogram  
https://www.boost.org/doc/libs/1_72_0/doc/html/accumulators/user_s_guide.html#accumulators.user_s_guide.the_statistical_accumulators_library.rolling_mean  
~as explained in the user guide~  
  
The implemention seems to be non-trivial and we also have to write tests and documentation. So I think it is better to close this and instead add a sentence to the user guide here  
https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/histogram/guide.html#histogram.guide.expert.user_defined_accumulators  
that explicitly points to the accumulators in Boost.Accumulators.

📁 include/boost/histogram/accumulators/rolling_mean.hpp

```diff
  20 |+ 
  21 |+ template <class T, class Allocator = std::allocator<T>>
  22 |+ struct circular_buffer
```

> Username: HDembinski  
> Created_at: 2020-03-05 09:28:52 UTC  
> Updated_at: 2020-03-06 09:07:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/269#discussion_r388170967  

We should not copy a full circular_buffer implementation, because we hardly use the full interface and the implementation is hidden inside the accumulator. A very simple implementation of the used functionality should be put in place to reduce the compile-time cost of parsing this code.

---

📁 include/boost/histogram/accumulators/rolling_mean.hpp

```diff
 205 |+ 
 206 |+ template <class ValueType>
 207 |+ struct rolling_mean
```

> Username: HDembinski  
> Created_at: 2020-03-05 09:37:04 UTC  
> Updated_at: 2020-03-06 09:07:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/269#discussion_r388175629  

The rolling mean should allow people to use any allocator and it should allow people to use a fixed size buffer. This is necessary for people who want to get the last bit of performance and embedded systems. The template signature could look like this:  
  
```  
template <class ValueType = double, class AllocatorOrBufferSize = std::allocator<ValueType>>  
struct rolling_mean { ... };  
```  
So that people can do  
```  
rolling_mean()  
rolling_mean<double, my_allocator<double>>(3);  
rolling_mean<double, my_allocator<double>>(3, my_allocator<double>());  
rolling_mean<double, 3>(3); // no heap allocation, uses a static buffer of size 3; passing the window_size is not redundant, see below  
rolling_mean<double, 5>(3); // using a window_size smaller than the buffer size is allowed

---

📁 include/boost/histogram/accumulators/rolling_mean.hpp

```diff
 242 |+   /// Return rolling mean of accumulated samples
 243 |+   const_reference value() const noexcept { return value_; }
 244 |+ 
```

> Username: HDembinski  
> Created_at: 2020-03-05 09:41:01 UTC  
> Updated_at: 2020-03-06 09:07:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/269#discussion_r388177928  

A variance estimate should be returned as well.


---

## Comment 3

> Username: HDembinski  
> Created_at: 2020-03-06 09:22:53 UTC  
> Url: https://github.com/boostorg/histogram/pull/269#issuecomment-595678585  

@jbuonagurio Do you agree with closing this?

---

## Comment 4

> Username: jbuonagurio  
> Created_at: 2020-03-06 19:34:03 UTC  
> Url: https://github.com/boostorg/histogram/pull/269#issuecomment-595926256  

> @jbuonagurio Do you agree with closing this?  
  
@HDembinski that's up to you, this PR was per your request in #250. They are important to me, but are rolling calculations important enough to other users to warrant an implementation at the library-level? I could continue work on this, adding support for variance calculation and compile-time fixed-size buffers. The circular buffer implementation is close to as simple as it gets unless I use something like `std::deque` which would probably impact performance, or depend on `boost::circular_buffer` (as Boost.Accumulators does).

---

## Comment 5

> Username: HDembinski  
> Created_at: 2020-03-07 13:42:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/269#issuecomment-596089879  

I thought it is a good idea at first, but now it looks like we are rebuilding the rolling mean from Boost.Accumulators, which does not sound like a good investment of our time. The library-included accumulators mean, weighted_mean, etc are also duplications of code in Boost.Accumulatos, but they are so common and so simple that they can be implemented without relying on Boost.Accumulators explicitly. However, this should be rather the exception than the rule. Code duplication is bad.

---
