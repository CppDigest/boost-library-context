# #64 Finish proofreading pass. [Merged]

> Username: nat-goodspeed  
> Created at: 2015-09-03 13:27:52 UTC  
> Updated at: 2016-06-06 11:36:34 UTC  
> Merged at: 2015-09-03 14:02:37 UTC  
> Closed at: 2015-09-03 14:02:37 UTC  
> Url: https://github.com/boostorg/fiber/pull/64  

See d653cbdcc notes for details.  
  
Along the way, I realized promise_handler should be using std::shared_ptr and std::make_shared instead of boost::shared_ptr -- see bcdb55582 notes.

---
