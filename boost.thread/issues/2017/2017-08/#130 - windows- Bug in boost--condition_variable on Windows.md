# #130 - windows: Bug in boost::condition_variable on Windows [Closed]

> Username: panyusko  
> Created at: 2017-08-23 17:04:04 UTC  
> Updated at: 2017-08-26 09:44:57 UTC  
> Closed at: 2017-08-26 09:44:57 UTC  
> Url: https://github.com/boostorg/thread/issues/130  

The bug was made in commit https://github.com/boostorg/thread/commit/ace2b8f89ec398654e9f696d26accbb0b8039bdb (see line 247)  
```  
// do it here to avoid throwing on the destructor  
entry->remove_waiter();  
```  
Instead should be `entry.remove_waiter();` because of **entry_manager** has overloaded operator -> and the function is called on **basic_cv_list_entry** but not **entry_manager** itself. This results in synchronization issues.

---

## Comment 1

> Username: viboes  
> Created at: 2017-08-24 05:00:31 UTC  
> Url: https://github.com/boostorg/thread/issues/130#issuecomment-324533324  

I suspect that this was already fixed, isn't it?

---

## Comment 2

> Username: viboes  
> Created at: 2017-08-24 06:43:22 UTC  
> Url: https://github.com/boostorg/thread/issues/130#issuecomment-324548069  

You are right. Using the same name for the function was an source of errors.  
  
Thanks for catching this.

---

## Comment 3

> Username: viboes  
> Created at: 2017-08-26 09:44:57 UTC  
> Url: https://github.com/boostorg/thread/issues/130#issuecomment-325108862  

https://github.com/boostorg/thread/commit/a02f0ec577644ca45cdca20e91cd8cd82e6c2017
