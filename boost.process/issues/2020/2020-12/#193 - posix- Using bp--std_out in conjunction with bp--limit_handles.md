# #193 - posix: Using bp::std_out in conjunction with bp::limit_handles [Open]

> Username: ceggers-arri  
> Created at: 2020-12-18 15:33:37 UTC  
> Updated at: 2021-10-11 18:33:29 UTC  
> Url: https://github.com/boostorg/process/issues/193  

Boost version: 1.72.0  
Platform: Linux  
  
I use `bp::limit_handles` in order to avoid leaking file descriptors to child processes. This works fine, but I was unsuccessful combining this with redirection of `std_out` to a `string` `future`.  
  ```  
 /* works fine */  
std::vector<char> stdOut(4096);  
auto child = bp::system("/bin/echo", "1",  
                          bp::std_out > boost::asio::buffer(stdOut),  
                          bp::limit_handles);  
  
/* string is empty */  
std::future<std::string> stdOut;  
auto child = bp::system("/bin/echo", "1",  
                          bp::std_out >stdOut,  
                          bp::limit_handles);  
  
```  
In the second example, the `future` is valid, but the `string` is empty. When I remove `bp::limit_handles`, `stdOut` contains `"1\n"`.  
  
I recognized that there is a slight difference between `async_out_buffer` ...  
```  
template<int p1, int p2, typename Buffer>  
struct async_out_buffer : ::boost::process::detail::posix::handler_base_ext,  
                          ::boost::process::detail::posix::require_io_context,  
                          ::boost::process::detail::uses_handles  
{  
...  
```  
... and `async_out_future`:  
```  
template<int p1, int p2, typename Type>  
struct async_out_future : ::boost::process::detail::posix::handler_base_ext,  
                          ::boost::process::detail::posix::require_io_context  
{  
...  
```  
  
While the former derives from `bp::detail::use_handles` (which seems to avoid closing this file descriptor), the latter doesn't do this.  
  
Is this a by intention or a bug? Is there a way redirecting `std_out` to a `string` together with closing all other file handles?  
  
**Side question:** How can I determine the number of output bytes for the first example (with the vector)?  
  
regards  
Christian

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-12-19 01:37:42 UTC  
> Url: https://github.com/boostorg/process/issues/193#issuecomment-748399246  

This is a bug. thanks for reporting.

---

## Comment 2

> Username: garethsb  
> Created at: 2021-10-11 14:46:42 UTC  
> Updated at: 2021-10-11 15:06:27 UTC  
> Url: https://github.com/boostorg/process/issues/193#issuecomment-940097416  

@klemens-morgenstern also bug for `async_pipe`... https://github.com/boostorg/process/blob/master/include/boost/process/detail/posix/pipe_out.hpp is also missing `uses_handles`?  
  
This commit https://github.com/boostorg/process/commit/f8c0dd4da58a2cf5db110d5a7e116adbe09f24a8 seems to have changes for `pipe_out`... but this wasn't taken in the merge commit https://github.com/boostorg/process/commit/fa81cecffc16fc557a3b99bcf8ecb6a7c9825937 😞

---

## Comment 3

> Username: garethsb  
> Created at: 2021-10-11 15:34:30 UTC  
> Updated at: 2021-10-11 18:33:29 UTC  
> Url: https://github.com/boostorg/process/issues/193#issuecomment-940136542  

I spotted a few other issues with `limit_handles` implementation...  
  
This seems to be an important typo, `on_success` presumably intended:  
  
https://github.com/boostorg/process/blob/2b39b56efb1cc7a3efe8d8815868ed0060111e7a/include/boost/process/detail/windows/handles.hpp#L165  
  
This doc seems to be untrue (ah, already reported as https://github.com/boostorg/process/issues/212):  
  
https://github.com/boostorg/process/blob/8222a57744a5df9beddcc7bddc168af66c85e3c7/include/boost/process/handles.hpp#L94-L99  
  
This doc is confusing:  
  
https://github.com/boostorg/process/blob/8222a57744a5df9beddcc7bddc168af66c85e3c7/include/boost/process/handles.hpp#L85  
  
I assume that's meant to be "sets all **handles** to be inherited only **explicitly**"?  
  
This comment has a typo, `get_used_handles` presumably intended:  
  
https://github.com/boostorg/process/blob/2b39b56efb1cc7a3efe8d8815868ed0060111e7a/include/boost/process/detail/used_handles.hpp#L25
