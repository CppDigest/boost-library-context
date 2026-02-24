# #99 - queue_back_view: documentation mismatch [Closed]

> Username: dvd0101  
> Created at: 2016-09-06 13:26:05 UTC  
> Updated at: 2017-03-01 07:08:12 UTC  
> Closed at: 2016-11-05 22:35:22 UTC  
> Url: https://github.com/boostorg/thread/issues/99  

The documentation says that `queue_back_view` supports  the [pull methods](https://github.com/boostorg/thread/blob/develop/doc/sync_queues_ref.qbk#L773) while they are not present in the implementation.

---

## Comment 1

> Username: viboes  
> Created at: 2016-09-06 17:27:45 UTC  
> Url: https://github.com/boostorg/thread/issues/99#issuecomment-245025535  

Thanks for caching this error in the documentation.  
  
A queue_back/front_view are useful to split the push and the pull functionalities.  
  
I will try to fix it for the next version.

---

## Comment 2

> Username: viboes  
> Created at: 2016-09-06 18:12:29 UTC  
> Url: https://github.com/boostorg/thread/issues/99#issuecomment-245039289  

https://github.com/boostorg/thread/commit/866ff746ae6b5c90c29dde7323fd5d6f36adbb30
