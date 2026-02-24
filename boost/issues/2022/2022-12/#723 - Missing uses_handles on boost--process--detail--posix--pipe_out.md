# #723 - Missing uses_handles on boost::process::detail::posix::pipe_out [Open]

> Username: bambu123  
> Created at: 2022-12-15 01:30:03 UTC  
> Updated at: 2022-12-15 03:02:38 UTC  
> Url: https://github.com/boostorg/boost/issues/723  

The boost::process::detail::posix::pipe_out are not inheriting boost::process::detail::uses_handles.  
The descriptors created by the pipe are closed by limit_handles on the forked child process.

---

## Comment 1

> Username: bambu123  
> Created at: 2022-12-15 03:02:38 UTC  
> Url: https://github.com/boostorg/boost/issues/723#issuecomment-1352495478  

The _pipe_sink for error in the executor is also closed by limit_handles.
