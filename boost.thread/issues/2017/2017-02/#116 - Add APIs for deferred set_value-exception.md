# #116 - [Proposal] Add APIs for deferred set_value/exception [Closed]

> Username: jamboree  
> Created at: 2017-02-27 10:23:35 UTC  
> Updated at: 2017-09-28 21:54:12 UTC  
> Closed at: 2017-09-28 21:54:12 UTC  
> Url: https://github.com/boostorg/thread/issues/116  

As discussed on the mailing list:  
  
http://lists.boost.org/boost-users/2017/02/87211.php  
  
The proposal is to add these functions:  
* `promise::set_value_deferred`  
* `promise::set_exception_deferred`  
* `promise::notify_deferred`  
  
It's for finer-grain control. The existing `set_xxx_at_thread_exit` can thus be implemented in terms of these functions (where at thread-exit, it calls `notify_deferred` automatically).  
This allows using the shared-state's storage w/o additional space overhead on the user side.

---

## Comment 1

> Username: viboes  
> Created at: 2017-09-18 16:25:44 UTC  
> Url: https://github.com/boostorg/thread/issues/116#issuecomment-330277134  

Here it is a PR https://github.com/boostorg/thread/pull/150.  
  
Please, could you check if this is what you was expecting?

---

## Comment 2

> Username: jamboree  
> Created at: 2017-09-19 01:33:05 UTC  
> Url: https://github.com/boostorg/thread/issues/116#issuecomment-330402036  

Yes, looks what I expected. Thanks!
