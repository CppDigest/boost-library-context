# #141 - boost::fibers::async problem [Closed]

> Username: jxfwinter  
> Created at: 2017-08-25 06:43:19 UTC  
> Updated at: 2017-08-25 07:30:28 UTC  
> Closed at: 2017-08-25 07:30:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/141  

I test boost 1.65 fiber example future.cpp，boost::fibers::async throw exception,  
the error is  
terminate called after throwing an instance of 'boost::fibers::packaged_task_uninitialized'  
  what():  Operation not permitted on an object without an associated state.  
  
boost 1.63 fiber boost::fibers::async has not the problem

---

## Comment 1

> Username: olk  
> Created at: 2017-08-25 07:30:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/141#issuecomment-324845361  

fixed in branach develop - unfortunately I got not the permission to add the fixes to 1.65 during the release phase  
  
boost.context:  
- https://github.com/boostorg/context/commit/420554fe9ac01482990e8ae314f4f7ba81010c9a  
  
boost.fiber:  
- https://github.com/boostorg/fiber/commit/63f1041f0e484e64dec437a0fa780ea2463abda2
