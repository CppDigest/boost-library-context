# #314 - [v2] vfork_launcher undefined behaviour [Closed]

> Username: gitmodimo  
> Created at: 2023-05-10 13:09:48 UTC  
> Updated at: 2023-06-28 12:24:17 UTC  
> Closed at: 2023-06-28 12:24:16 UTC  
> Url: https://github.com/boostorg/process/issues/314  

In my application stating of multiple processes with vfork_launcher causes exception:  
```  
terminate called after throwing an instance of 'boost::wrapexcept<boost::system::system_error>'  
  what():  assign: Bad file descriptor [system:9 at ...aarch64-buildroot-linux-gnu/sysroot/usr/include/boost/asio/detail/impl/reactive_descriptor_service.ipp:120:5 in function 'boost::system::error_code boost::asio::detail::reactive_descriptor_service::assign(boost::asio::detail::reactive_descriptor_service::implementation_type&, const native_handle_type&, boost::system::error_code&)']  
```  
It seems it is caused by undefined behaviour triggered by child process memory modification. [After vfork it is forbidden to modify any memory contents except vfrok return value](https://man7.org/linux/man-pages/man2/vfork.2.html) :  
  
```  
pid = ::vfork();  
if (pid == -1)  
{  
    ...  
}  
else if (pid == 0)  
{  
    ctx.notify_fork(BOOST_PROCESS_V2_ASIO_NAMESPACE::execution_context::fork_child);//undefined behaviour??  
    ec = detail::on_exec_setup(*this, executable, argv, inits...);  
    if (!ec)  
        close_all_fds(ec);//undefined behaviour??  
    if (!ec)  
        ::execve(executable.c_str(), const_cast<char * const *>(argv), const_cast<char * const *>(env));  
  
    BOOST_PROCESS_V2_ASSIGN_EC(ec, errno, system_category())  
    detail::on_exec_error(*this, executable, argv, ec, inits...);  
    ::exit(EXIT_FAILURE);  
    return basic_process<Executor>{exec};  
}  
ctx.notify_fork(BOOST_PROCESS_V2_ASIO_NAMESPACE::execution_context::fork_parent);  
```  
To fix this I removed `notify_fork`. It seems completely unnecessary since we execve and we do not hold any io_context. Or am I missing something?  
Also `close_all_fds` also modifies memory but it seems there is no problem with that.

---

## Comment 1

> Username: gitmodimo  
> Created at: 2023-05-11 09:45:59 UTC  
> Url: https://github.com/boostorg/process/issues/314#issuecomment-1543682828  

Also since child process uses parent's memory we should use _exit instead of exit. Otherwise we will mess up parents process state.  
This patch fixed all my issues for now.  
  
[0001-Fix-vfork_launcher.patch](https://github.com/boostorg/process/files/11450679/0001-Fix-vfork_launcher.patch)
