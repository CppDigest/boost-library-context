# #468 - io_context.run() block indefinitely if child redirects it's output to pipe [Open]

> Username: o01eg  
> Created at: 2025-03-21 17:24:56 UTC  
> Updated at: 2025-04-14 15:57:37 UTC  
> Url: https://github.com/boostorg/process/issues/468  

I have the code already using boost.process v1 to manage child processes:  
  
https://github.com/freeorion/freeorion/blob/a72caab5816e7401247ed0ea2399efe47ad6702a/util/Process.cpp#L215-L220  
  
If I add `boost::process::async_pipe` and redirects `boost::process::std_out` and `boost::process::std_err` here it freezes on `io_context.run()`.  
  
I've tried to use Boost.Process V2 but it doesn't help https://github.com/o01eg/freeorion/tree/boost-process-v2-capture

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-04-14 15:57:36 UTC  
> Url: https://github.com/boostorg/process/issues/468#issuecomment-2802176628  

Well yes, io_context will process any IO/ i.e. block until both pipes are closed.
