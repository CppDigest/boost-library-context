# #261 - Resource leak @ https://github.com/boostorg/thread/blob/boost-1.61.0/include/boost/thread/detail/thread.hpp#L859 [Closed]

> Username: venjun2000  
> Created at: 2019-01-02 08:45:38 UTC  
> Updated at: 2019-03-24 12:37:03 UTC  
> Closed at: 2019-03-24 12:37:03 UTC  
> Url: https://github.com/boostorg/thread/issues/261  

Issue location:  
https://github.com/boostorg/thread/blob/boost-1.61.0/include/boost/thread/detail/thread.hpp#L859  
  
Coverity tool detects and reports as major issue   
  
Resource leak    /DeliSpace/TegraP1Lin.Rel/External/Boost/inc/boost/thread/detail/thread.hpp    at_thread_exit

---

## Comment 1

> Username: viboes  
> Created at: 2019-01-03 07:01:37 UTC  
> Url: https://github.com/boostorg/thread/issues/261#issuecomment-451067983  

Thanks for the report. Have you analyzed the report? Are you sure there is a real leak? How are you sure this function 'thread_exit_func' is not called? isn't this a false positive?

---

## Comment 2

> Username: venjun2000  
> Created at: 2019-01-03 11:22:31 UTC  
> Url: https://github.com/boostorg/thread/issues/261#issuecomment-451116796  

@viboes ,  
Here's the report..  
  
840        void BOOST_THREAD_DECL make_ready_at_thread_exit(shared_ptr<shared_state_base> as);  
841#endif  
842    }  
843  
844    namespace this_thread  
845    {  
846        template<typename F>  
847        void at_thread_exit(F f)  
848        {  
   	**1. alloc_fn: Storage is returned from allocation function heap_new. [show details]  
   	2. var_assign: Assigning: thread_exit_func = storage returned from boost::detail::heap_new(f).**  
849            detail::thread_exit_function_base* const thread_exit_func=detail::heap_new<detail::thread_exit_function<F> >(f);  
   	**3. noescape: Resource thread_exit_func is not freed or pointed-to in unimplemented function add_thread_exit_function.**  
850            detail::add_thread_exit_function(thread_exit_func);  
   	  
**CID 1588763 (#1 of 1): Resource leak (RESOURCE_LEAK)  
4. leaked_storage: Variable thread_exit_func going out of scope leaks the storage it points to.**  
851        }  
852    }  
853}

---

## Comment 3

> Username: viboes  
> Created at: 2019-01-03 17:59:34 UTC  
> Url: https://github.com/boostorg/thread/issues/261#issuecomment-451225302  

I don't see from the report why there is a memory leak.

---

## Comment 4

> Username: venjun2000  
> Created at: 2019-01-04 10:33:41 UTC  
> Url: https://github.com/boostorg/thread/issues/261#issuecomment-451408779  

>>I don't see from the report why there is a memory leak.  
  
Can you confirm whether there is leak or is it false positive ?    Is it something like the user of this library will free the storage allocated ? Is this library designed like this ?

---

## Comment 5

> Username: viboes  
> Created at: 2019-01-06 15:14:22 UTC  
> Url: https://github.com/boostorg/thread/issues/261#issuecomment-451749090  

The library free the storage at thread exit. Is Coverity checking it after the thread exits? IIUC Coverity is a static analysis tool, not a run-time one. I don't know how it can tell for sure that this function will not be called.

---

## Comment 6

> Username: austin-beer  
> Created at: 2019-03-23 21:01:48 UTC  
> Url: https://github.com/boostorg/thread/issues/261#issuecomment-475904398  

I ran `valgrind --leak-check=full test_thread_exit` (test_thread_exit.cpp is in the Boost.Thread test folder) against the latest develop branch and it reported that 0 bytes were leaked. The Coverity finding is a false positive. The memory is freed here:  
  
https://github.com/boostorg/thread/blob/develop/src/pthread/thread.cpp#L104
