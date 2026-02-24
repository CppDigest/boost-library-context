# #470 - [Windows] Setting the capacity of the underline pipe [Open]

> Username: PolarGoose  
> Created at: 2025-03-21 19:28:13 UTC  
> Updated at: 2025-07-14 02:44:31 UTC  
> Url: https://github.com/boostorg/process/issues/470  

I have encountered a similar issue to [Setting the capacity of the underline pipe. Linux only](https://github.com/boostorg/process/issues/322) but on Windows.  
  
My code is the following:  
```  
void RunProcess(const std::string& exePath, std::vector<std::string> args) {  
  boost::asio::io_context ctx;  
  boost::asio::readable_pipe stdOutPipe(ctx), stdErrPipe(ctx);  
  boost::process::v2::process proc(ctx, exePath, args, boost::process::v2::process_stdio{ {}, stdOutPipe, stdErrPipe }, boost::process::v2::windows::show_window_hide);  
  /* ... */  
```  
  
The `readable_pipe` gets a native pipe of only 8kb here: [connect_pipe.ipp::create_pipe](https://github.com/boostorg/asio/blob/2af02fa2c817a61c30762713d791962e448e35b4/include/boost/asio/impl/connect_pipe.ipp#L71C13-L71C23)  
  
8kb is very little if we need to get a lot of input from a process because the process will have to get blocked constantly until the pipe is cleared.  
  
Is it possible to increase the pipe buffer size?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-03-26 12:22:34 UTC  
> Url: https://github.com/boostorg/process/issues/470#issuecomment-2754223103  

Yes, just use `stdOutPipe.native_handle()` with `fcntl`.

---

## Comment 2

> Username: PolarGoose  
> Created at: 2025-03-26 12:46:51 UTC  
> Updated at: 2025-03-26 13:05:22 UTC  
> Url: https://github.com/boostorg/process/issues/470#issuecomment-2754285352  

@klemens-morgenstern,  
  
Thank you for the response. I saw that `fcntl` can work for Linux.  
Is it available on Windows?

---

## Comment 3

> Username: PolarGoose  
> Created at: 2025-07-14 00:06:09 UTC  
> Updated at: 2025-07-14 02:44:31 UTC  
> Url: https://github.com/boostorg/process/issues/470#issuecomment-3067413766  

Update:  
Unfortunately, so far, the best option I have found is to monkey-patch the source code of boost-process. For my project, I created a custom vcpkg port of `boost-process` with a modified `connect_pipe.ipp::create_pipe` method.
