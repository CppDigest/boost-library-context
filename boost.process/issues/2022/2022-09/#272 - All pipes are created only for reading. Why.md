# #272 - [Windows][Pipe] All pipes are created only for reading. Why? [Open]

> Username: leeroy-Jk  
> Created at: 2022-09-12 15:13:27 UTC  
> Updated at: 2022-09-20 05:15:20 UTC  
> Url: https://github.com/boostorg/process/issues/272  

Hello, you write in documentation async_pipe is used and for reading, and for writing:  
> It can be used directly with boost::asio::async_read or async_write.  
  
But I've saw in your source code async_pipe is created with `PIPE_ACCESS_INBOUND` mode. It means async_pipe is available only for reading from the server side. I didn't understand a long time, why my code couldn't work with two pipes with the same name in two unrelated process.  
  
May be does it need to write this in docs and the fact that async_pipe couldn't be used as IPC-like named pipe?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-09-20 05:15:20 UTC  
> Url: https://github.com/boostorg/process/issues/272#issuecomment-1251850355  

Probably should be documented,the pipe is only meant to be used similar to posix pipes. The only reason they're named is because anonymous pipes on windows don't allow overlapped IO.
