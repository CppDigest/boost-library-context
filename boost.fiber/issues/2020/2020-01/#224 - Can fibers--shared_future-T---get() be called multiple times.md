# #224 - Can fibers::shared_future<T>::get() be called multiple times? [Closed]

> Username: ingomueller-net  
> Created at: 2020-01-03 16:01:46 UTC  
> Updated at: 2020-01-13 07:24:15 UTC  
> Closed at: 2020-01-13 07:24:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/224  

I suppose that the answer is yes; it seems like that is the point of `shared_future`. See also [this SO discussion](https://stackoverflow.com/questions/56756112/is-it-legal-to-call-shared-futureget-multiple-times-on-the-same-instance-in) why it seems to be allowed in `boost::shared_future` and `std::shared_future`. Finally, I tried it out and it seems to work.  
  
However, this seems to contradict the documentation of [`shared_future<T>::get()`](https://www.boost.org/doc/libs/1_72_0/libs/fiber/doc/html/fiber/synchronization/futures/future.html#shared_future_get), which defines the post-condition `false == valid()`. Is the documentation wrong or am I misunderstanding something?

---

## Comment 1

> Username: olk  
> Created at: 2020-01-13 07:24:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/224#issuecomment-573537795  

yes - docu mus be fixed
