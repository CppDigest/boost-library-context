# #391 - Investigate RAII event synchronization guard object [Closed]

> Username: kylelutz  
> Created at: 2014-12-29 19:23:00 UTC  
> Updated at: 2017-04-24 16:20:44 UTC  
> Closed at: 2017-04-24 16:20:44 UTC  
> Url: https://github.com/boostorg/compute/issues/391  

Investigate adding an event guard object for synchronizing asynchronous events automatically upon destruction.  
  
From the review:  
  
I guess it'd be nice to have a scope guard that blocks on boost::compute::event.  
  
```  
std::vector<int> hv(16000000);  
std::generate(hv.begin(), hv.end(), rand);  
boost::compute::vector<float> dv(host_vector.size(), context);  
boost::compute::guard wg(cq.enqueue_write_buffer_async(dv, 0, hv.size(), &hv.front());  
  
// do some device -> device operations  
  
boost::compute::guard rg(cq.enqueue_read_buffer_async(dv, 0, hv.size(), &hv.front());  
```  
  
[Short names used for the sake of email.]  
  
boost::compute::guard would do nothing more than hold a reference to the event and call boost::compute::event::wait(), upon destruction (if it contains a valid event, at that point).  
  
So, if an exception is thrown after wg is constructed, then the stack unwind blocks on its completion (or failure).  Similarly, if the exception is thrown after rg, then the unwind blocks on both wg and rg.  
  
Obviously, this example is somewhat artificial.  If the read were really the last operation in scope, then you could just use a synchronous copy.  But, I think one strong use case for async copies is to free up device memory for subsequent operations.  So, I might transfer something off, even if I'm not going to do anything with it, at that point.  
##

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-04-24 16:20:44 UTC  
> Url: https://github.com/boostorg/compute/issues/391#issuecomment-296726776  

I think it's implemented in [boost/compute/async/wait_guard.hpp](https://github.com/boostorg/compute/blob/master/include/boost/compute/async/wait_guard.hpp).
