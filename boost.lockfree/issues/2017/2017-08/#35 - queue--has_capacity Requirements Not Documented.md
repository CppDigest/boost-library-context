# #35 - queue::has_capacity Requirements Not Documented [Closed]

> Username: RobertLeahy  
> Created at: 2017-08-04 17:32:41 UTC  
> Updated at: 2019-12-06 14:40:50 UTC  
> Closed at: 2019-12-06 14:40:50 UTC  
> Url: https://github.com/boostorg/lockfree/issues/35  

The constructors for `queue` do not document their requirements with respect to the `capacity` tag.  
  
Moreover [in certain constructors a runtime assert (only present in debug mode) are used to verify the requirement](https://github.com/boostorg/lockfree/blob/8e98981c211be6a6970a5a954c0a50d4ab736c45/include/boost/lockfree/queue.hpp#L187). It shouldn't be necessary to build code in debug mode to discover what is logically a compile time constraint.

---

## Comment 1

> Username: austin-beer  
> Created at: 2019-12-03 15:25:25 UTC  
> Url: https://github.com/boostorg/lockfree/issues/35#issuecomment-561217380  

The reason for using runtime asserts is explained in #43.
