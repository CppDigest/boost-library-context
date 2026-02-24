# #308 - Fortify scan - buffer overflow - Boost version 1.63 [Closed]

> Username: TejasSonawan  
> Created at: 2018-10-31 15:44:42 UTC  
> Updated at: 2020-12-30 01:12:04 UTC  
> Closed at: 2020-12-30 01:12:03 UTC  
> Url: https://github.com/boostorg/asio/issues/308  

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

> Username: swatanabe  
> Created at: 2018-10-31 16:09:17 UTC  
> Url: https://github.com/boostorg/asio/issues/308#issuecomment-434747846  

AMDG  
  
a) This should be filed against ASIO.  Tickets filed  
   against the superproject are likely to be ignored.  
b) I believe that this is technically undefined behavior  
   and should be fixed.  However, there is in fact enough  
   memory allocated, so depending on the compiler, it  
   may work without issues.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: mclow  
> Created at: 2018-10-31 16:10:30 UTC  
> Url: https://github.com/boostorg/asio/issues/308#issuecomment-434748299  

The place to file bugs against ASIO is http://github.com/boostorg/asio

---

## Comment 3

> Username: TejasSonawan  
> Created at: 2018-10-31 17:08:35 UTC  
> Url: https://github.com/boostorg/asio/issues/308#issuecomment-434769883  

Thanks for your reply, This is just an example, there are other issue reported Fortify for boost. i have not mentioned them all.

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 01:12:02 UTC  
> Url: https://github.com/boostorg/asio/issues/308#issuecomment-752293297  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#735](https://github.com/chriskohlhoff/asio/issues/735).
