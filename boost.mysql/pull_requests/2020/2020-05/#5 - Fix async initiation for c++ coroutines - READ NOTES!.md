# #5 Fix async initiation for c++ coroutines - READ NOTES! [Closed]

> Username: madmongo1  
> Created at: 2020-05-21 09:56:59 UTC  
> Updated at: 2020-07-06 18:18:14 UTC  
> Closed at: 2020-07-06 18:18:14 UTC  
> Url: https://github.com/boostorg/mysql/pull/5  

My son and I have done some quick and dirty surgery to ensure that async initiation is correct for all completion tokens, including use_awaitable.  
  
I have not updated any tests, but we have tested in a program running locally.  
  
Summary of Changes:  
  
- Refactored async ops to use async_compose (this was easier than converting for async_initiate since none of them involved concurrent async sub-operations)  
- Added executor_type and get_executor to channel and result_set types. This is necessary for async_compose and is good manners for an asio io_object in any case.  
- Destroyed some of your careful formatting by accident (sorry about that).  
  
Comments:  
  
- Way too many little typedefs and utility base-classes. async_op in particular really has no useful purpose now that async_compose is taking care of initiation and completion hander storage and invocation.  
  
- For library maintainers these little one-liner utilities may seem like useful packaging, but in reality they just complicate the maintenance process because they require maintainers to understand your internal api before they can do work. Sometimes it's better just to explicitly name types and call functions.

---
