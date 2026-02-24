# #336 Code typos fixes [Merged]

> Username: AJIOB  
> Created at: 2023-08-19 12:02:56 UTC  
> Updated at: 2023-10-05 05:21:41 UTC  
> Merged at: 2023-10-05 01:36:50 UTC  
> Closed at: 2023-10-05 01:36:51 UTC  
> Url: https://github.com/boostorg/process/pull/336  

@klemens-morgenstern , please recheck the [Subprocess environment](https://www.boost.org/doc/libs/1_83_0/doc/html/boost_process/v2.html#boost_process.v2.env.process_env) example again.  
  
Looks like rows `process proc(ctx, exe, {"main.cpp"}, process_environment(my_env));` row still may not be compiled.

---
