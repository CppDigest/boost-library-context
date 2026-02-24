# #31 - destructor called on non-final.... [Closed]

> Username: vinniefalco  
> Created at: 2017-08-10 00:05:15 UTC  
> Updated at: 2017-08-10 21:48:44 UTC  
> Closed at: 2017-08-10 17:26:31 UTC  
> Url: https://github.com/boostorg/coroutine/issues/31  

Any ideas on this error?  
```  
../boost/coroutine/detail/push_coroutine_object.hpp:247:9: warning: destructor called on  
non-final 'boost::coroutines::detail::push_coroutine_object<boost::coroutines::pull_coroutine<void>,  
void, boost::asio::detail::coro_entry_point<boost::asio::detail::wrapped_handler<  
boost::asio::io_service::strand, void (*)(), boost::asio::detail::is_continuation_if_running>,(lambda  
at ../libs/beast/test/extras/include/boost/beast/test/yield_to.hpp:123:9)> &,  
boost::coroutines::basic_standard_stack_allocator<boost::coroutines::stack_traits> >' that  
has virtual functions but non-virtual destructor [-Wdelete-non-virtual-dtor]  
```  
  
Coming from:  
http://www.boost.org/development/tests/develop/output/teeks99-02-dc3-9-14-Docker-64on64-beast-clang-linux-3-9~c++14-warnings.html#buffered_read_stream  
  
Thanks

---

## Comment 1

> Username: olk  
> Created at: 2017-08-10 06:00:15 UTC  
> Url: https://github.com/boostorg/coroutine/issues/31#issuecomment-321458750  

seams that push_coroutine_impl needs a virtual dtor

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-10 13:54:40 UTC  
> Url: https://github.com/boostorg/coroutine/issues/31#issuecomment-321557880  

Ah, great - sounds like an easy fix!

---

## Comment 3

> Username: olk  
> Created at: 2017-08-10 17:26:31 UTC  
> Url: https://github.com/boostorg/coroutine/issues/31#issuecomment-321618969  

fixed

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-08-10 21:48:44 UTC  
> Url: https://github.com/boostorg/coroutine/issues/31#issuecomment-321682805  

This is going to clear up a TON of items in my boost test matrix, thanks!
