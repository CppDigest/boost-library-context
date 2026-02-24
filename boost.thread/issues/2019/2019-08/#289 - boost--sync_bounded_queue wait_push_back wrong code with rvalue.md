# #289 - boost::sync_bounded_queue wait_push_back wrong code with rvalue [Open]

> Username: Lokimed  
> Created at: 2019-08-15 07:55:44 UTC  
> Updated at: 2019-09-17 18:57:55 UTC  
> Url: https://github.com/boostorg/thread/issues/289  

lvalue code correct  
  
```javascript  
  template <typename ValueType>  
  queue_op_status sync_bounded_queue<ValueType>::wait_push_back(const ValueType& elem)  
  {  
    unique_lock<mutex> lk(mtx_);  
    return wait_push_back(elem, lk);  
  }  
```  
  
But rvalue code wrong   
  
```javascript  
  template <typename ValueType>  
  queue_op_status sync_bounded_queue<ValueType>::wait_push_back(BOOST_THREAD_RV_REF(ValueType) elem)  
  {  
      unique_lock<mutex> lk(mtx_);  
      return try_push_back(boost::move(elem), lk);  
  }  
```  
  
Instead  
return try_push_back(boost::move(elem), lk);  
must be  
return wait_push_back(boost::move(elem), lk);

---

## Comment 1

> Username: viboes  
> Created at: 2019-08-26 05:22:42 UTC  
> Url: https://github.com/boostorg/thread/issues/289#issuecomment-524719787  

Please, could you provide a por?

---

## Comment 2

> Username: Lokimed  
> Created at: 2019-08-26 07:21:30 UTC  
> Url: https://github.com/boostorg/thread/issues/289#issuecomment-524748277  

Hi. What you mean by word "por"? You need code example (test) which can detect problem?

---

## Comment 3

> Username: viboes  
> Created at: 2019-09-14 13:07:36 UTC  
> Url: https://github.com/boostorg/thread/issues/289#issuecomment-531478472  

Sorry a PR, pull request.

---

## Comment 4

> Username: Lokimed  
> Created at: 2019-09-17 18:57:55 UTC  
> Url: https://github.com/boostorg/thread/issues/289#issuecomment-532355743  

https://github.com/boostorg/thread/pull/292  
i hope i did pull request correct.
