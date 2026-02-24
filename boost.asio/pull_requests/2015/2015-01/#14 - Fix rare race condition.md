# #14 Fix rare race condition. [Closed]

> Username: ya1gaurav  
> Created at: 2015-01-30 10:09:01 UTC  
> Updated at: 2017-12-02 07:58:01 UTC  
> Closed at: 2017-12-02 07:58:01 UTC  
> Url: https://github.com/boostorg/asio/pull/14  

descriptor_data->mutex_ should be unlocked after descriptor data state is freed up as free_descriptor_state uses another lock to do its operation. While doing free, if some other thread get LOCK 1 i.e descriptor_data->mutex_, it may cause crash in rare condition , when LOCK2 i.e registered_descriptors_mutex_ frees some relevant member variable. In case of deregister_internal_descriptor, no need to unlock as scoped lock will get unlock, once it gets out of scope.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:58:01 UTC  
> Url: https://github.com/boostorg/asio/pull/14#issuecomment-348675913  

I believe this has already been fixed by a slightly different change.

---
