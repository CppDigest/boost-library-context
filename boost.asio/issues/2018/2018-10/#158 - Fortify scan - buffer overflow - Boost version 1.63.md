# #158 - Fortify scan - buffer overflow - Boost version 1.63 [Closed]

> Username: TejasSonawan  
> Created at: 2018-10-31 17:08:50 UTC  
> Updated at: 2020-12-30 00:54:13 UTC  
> Closed at: 2020-12-30 00:54:12 UTC  
> Url: https://github.com/boostorg/asio/issues/158  

Hello Team,  
  
We are using Boost 1.63 lib in our project and when we are doing static scan using Fortify tool,  
It showing buffer overflow error on the file "Boost/asio/detail/win_fd_set_adapter.hpp" on line 66.  
  
Below is code snippet:  
  
bool set(socket_type descriptor)  
{  
for (u_int i = 0; i < fd_set_->fd_count; ++i)  
if (fd_set_->fd_array[i] == descriptor)  
return true;  
  
reserve(fd_set_->fd_count + 1);  
fd_set_->fd_array[fd_set_->fd_count++] = descriptor;  
return true;  
}  
  
issue for "fd_set_->fd_array[fd_set_->fd_count++] = descriptor;" line.  
  
are these errors already fixed in a recent version or are you planning to fix them in a future version ?  
  
Thanks,  
-Tejas

---

## Comment 1

> Username: youngwolf-project  
> Created at: 2018-11-05 05:41:04 UTC  
> Url: https://github.com/boostorg/asio/issues/158#issuecomment-435760989  

How did you fix this issue, please?

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:54:11 UTC  
> Url: https://github.com/boostorg/asio/issues/158#issuecomment-752290096  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#647](https://github.com/chriskohlhoff/asio/issues/647).
