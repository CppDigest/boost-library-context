# #195 - abort if used with boost::asio::spawn() [Open]

> Username: olk  
> Created at: 2021-01-21 10:13:32 UTC  
> Updated at: 2021-01-21 10:13:32 UTC  
> Url: https://github.com/boostorg/process/issues/195  

After upgrade from 1.68 to 1.75 an abort is sometimes triggered.  
boost::process:child is used together with boost::asio:spawn() - sometimes a stackful coroutine that represents the child is sometimes resumed while it is already running.  
This is caused by using boost::asio::dispatch() instead of boost::asio:post() inside boost::process::detail::posix::sigchld_service::async_wait().  
docu: "The function object may be called from the current thread prior to returning from dispatch(). Otherwise, it is queued for execution."  
  
If agreed I'd like to provide a pull-request.
