# #225 - Reusing the same boost::process::async_pipe for both stderr and stdout [Closed]

> Username: linkret  
> Created at: 2021-10-13 07:15:24 UTC  
> Updated at: 2023-05-19 09:46:11 UTC  
> Closed at: 2023-05-19 07:53:47 UTC  
> Url: https://github.com/boostorg/process/issues/225  

I came across differing behavior under Windows and Linux. This code snippet works and runs fine under Windows, but throws an exception when trying to run on Linux (Debian10). I'm not sure if it's a bug or not, so I'm posting it here just in case. Perhaps this is not an issue at all. I was using boost 1.77, built with support for C++20, llvm.   
  
```  
_proc_ptr = std::make_unique<bp::child>(  
	bp::exe = cmd,  
	bp::args = args,  
	bp::std_in < stdin,  
	bp::std_out > _out,  
	bp::std_err > _out,  
	_ioc,  
	bp::on_exit = _exit_fun  
);  
```  
  
_out is a boost::process::async_pipe, and I use async_read_some() to initiate the reading of the process's std_out and std_err streams.   
  
Under Linux the process fails to launch, and throws a std::exception with the .what() message "Bad file descriptor". I tried it with several different executibles I tried to launch, including my own dummy one (which just echoes messages back with cerr and cout), SSH, and a few more.  
  
The workaround was for me to add another async_pipe (let's say _err), and say `bp::std_out > _out, bp::std_err > _err`, but my question is: why did the original code work under Windows? And should redirecting two streams into the same pipe work on Linux as well? Or is it expected behavior for it to not work? I wasn't able to find this information in the provided examples and documentation.   
  
Thank you.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2021-10-14 06:56:45 UTC  
> Url: https://github.com/boostorg/process/issues/225#issuecomment-943067521  

That's an interesting problem. Both close the used handle during the process launch, but apparently that doesn't work well on unix. Can you copy the the _out first?  
  
```cpp  
auto err = _out; //duplicate handles  
_proc_ptr = std::make_unique<bp::child>(  
	bp::exe = cmd,  
	bp::args = args,  
	bp::std_in < stdin,  
	bp::std_out > _out,  
	bp::std_err > err,  
	_ioc,  
	bp::on_exit = _exit_fun  
);  
  
```

---

## Comment 2

> Username: linkret  
> Created at: 2021-10-14 08:44:50 UTC  
> Updated at: 2021-10-14 09:10:42 UTC  
> Url: https://github.com/boostorg/process/issues/225#issuecomment-943145655  

Thanks for the prompt reply.   
  
I tried it, and I get the same runtime error again on unix. My _out is actually a std::shared_ptr, so I tried it with both `auto err = _out;` and `auto err = *_out;` (duplicating the shared_ptr, and duplicating the actual pipe object beneath it, respectively). (When I did this, I removed the _err member, which is used in the "workaround" solution.)  
  
I also tried the following: instead of constructing my _err member normally, I said _err(std::make_shared(*_out)), which creates a new async_pipe which is copy-constructed from my first one. When I try to do this, the exception that is thrown is again "Bad File Descriptor", thrown from:  
  
```  
async_pipe::async_pipe(const async_pipe& p) // the copy-constructor  
 -> _sink.  assign(sink); // the last line in the function  
   ->   void assign(const native_handle_type& handle)   
     ->    impl_.get_service().assign(impl_.get_implementation(), handle, ec); // the assign function uses the OS-API, which returns   
an error, which is stored into the ec as a "Bad file descriptor" error_code   
```  
  
Sorry for the ugly attempt at a stack-trace, but I hope it is clear enough for you to get the picture. Also, this last attempt doesn't work on Windows either. I get the same exception when trying to copy-construct the _err async_pipe from _out. Unlike the other cases, here I don't even manage to get to the part of the code where I try to create the process.   
  
Cheers,  
Leonard

---

## Comment 3

> Username: garethsb  
> Created at: 2021-10-18 14:37:06 UTC  
> Url: https://github.com/boostorg/process/issues/225#issuecomment-945843585  

Doesn't `(bp::std_out & bp::std_err) > _out` do what you wanted?

---

## Comment 4

> Username: linkret  
> Created at: 2021-10-18 15:24:00 UTC  
> Url: https://github.com/boostorg/process/issues/225#issuecomment-945890330  

> Doesn't `(bp::std_out & bp::std_err) > _out` do what you wanted?  
  
Sounds like it might, I'll try it tomorrow, thank you.

---

## Comment 5

> Username: jmccabe  
> Created at: 2023-05-18 20:39:10 UTC  
> Url: https://github.com/boostorg/process/issues/225#issuecomment-1553616812  

Did it?

---

## Comment 6

> Username: linkret  
> Created at: 2023-05-19 07:53:47 UTC  
> Url: https://github.com/boostorg/process/issues/225#issuecomment-1554187030  

It did, yes.

---

## Comment 7

> Username: jmccabe  
> Created at: 2023-05-19 09:46:10 UTC  
> Url: https://github.com/boostorg/process/issues/225#issuecomment-1554315250  

Thank you.
