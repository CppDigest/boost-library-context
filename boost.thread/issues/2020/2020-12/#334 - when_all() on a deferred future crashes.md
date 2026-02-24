# #334 - when_all() on a deferred future crashes [Open]

> Username: thisismiller  
> Created at: 2020-12-10 02:46:46 UTC  
> Updated at: 2020-12-10 02:46:46 UTC  
> Url: https://github.com/boostorg/thread/issues/334  

Trying to run  
  
```  
  int q = 0;  
  boost::promise<int> x;  
  boost::future<int> fx = x.get_future();  
  fx.then(boost::launch::deferred, [&q](boost::future<int>&&) { q = 1; });  
  future<std::tuple<boost::future<int>>>&& _ = boost::when_all(std::move(fx)); // CRASH!  
```  
  
Yields a crash:  
  
```  
#0  0x00007ffff4ae1387 in raise () from /lib64/libc.so.6  
#1  0x00007ffff4ae2a78 in abort () from /lib64/libc.so.6  
#2  0x00007ffff4ada1a6 in __assert_fail_base () from /lib64/libc.so.6  
#3  0x00007ffff4ada252 in __assert_fail () from /lib64/libc.so.6  
#4  0x000000000307ceaa in boost::shared_ptr<boost::detail::shared_state<int> >::operator-> (this=0xec0eb60)  
    at boost/smart_ptr/shared_ptr.hpp:734  
#5  0x0000000003094d85 in boost::future<int>::run_if_is_deferred (  
        this=0xec0eb60)  
    at boost/thread/future.hpp:1785  
#6  0x000000000308f5f7 in boost::detail::accumulate_run_if_is_deferred<std::tuple<boost::future<int> >, 1ul>::operator() (this=0x7fffffffd77f,   
        t=std::tuple containing)  
    at boost/thread/future.hpp:5685  
#7  0x0000000003086eb1 in boost::detail::future_when_all_tuple_shared_state<std::tuple<boost::future<int> >, boost::future<int>>::run_deferred() (  
        this=0xec0ea40)  
    at boost/thread/future.hpp:5726  
#8  0x000000000307d24c in boost::detail::future_when_all_tuple_shared_state<std:  
    :tuple<boost::future<int> >, boost::future<int>>::init() (this=0xec0ea40)  
        at boost/thread/future.hpp:5729  
#9  0x0000000003073cfd in boost::when_all<boost::future<int>>(boost::future<int>&&) (  
            f=<unknown type in unittests, CU 0xa5fc9, DIE 0x19fe8a>)  
    at boost/thread/future.hpp:5856  
```  
  
Which is maybe related to #112 asking for when_all to be rewritten anyway.
