# #62 - Flawed safe_dump_to(..) API [Closed]

> Username: cmannett85  
> Created at: 2018-09-02 17:03:48 UTC  
> Updated at: 2018-09-17 08:20:57 UTC  
> Closed at: 2018-09-17 08:20:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/62  

I'm trying to save a stacktrace to raw memory using the `safe_dump_to(void *, std::size_t)` function, however the function returns the number of stack frames (plus the termination frame) written - **not** the number of bytes written.  
  
This means that when I come to read this data, I have no idea what to put in the size field of `stacktrace::from_dump(const void *, std::size_t, const allocator_type &)`. At first I assumed that I could use `sizeof(boost::stacktrace::frame) * frames`, but the frame type contains heap allocated memory (`std::string`s) so that won't work.  
  
This SSCE demonstrates the problem:  
```  
#include <iostream>  
  
#include <boost/stacktrace.hpp>  
  
int main()  
{  
    auto buf = std::array<char, 8192>{};  
    const auto size = boost::stacktrace::safe_dump_to(buf.data(), buf.size());  
    const auto st = boost::stacktrace::stacktrace::from_dump(buf.data(), size);  
  
    std::cout << "ST from dump (" << size << " bytes): " << st << std::endl  
              << "ST: " << boost::stacktrace::stacktrace{} << std::endl;  
  
  
    return EXIT_SUCCESS;  
}  
```  
It outputs:  
```  
ST from dump (4 bytes):   
ST:  0# 0x00000000004016A7 in ./a.out  
 1# __libc_start_main in /lib/x86_64-linux-gnu/libc.so.6  
 2# 0x0000000000401759 in ./a.out  
```  
The second 'ST' line is just to show what is expected. The first line fails because I'm trying to treat the frame count as a byte size, which it obviously isn't.  
  
So how is the API supposed to be used?

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-09-13 15:03:54 UTC  
> Updated at: 2018-09-13 15:04:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/62#issuecomment-421039990  

You're right the API may be confusing.  
   
`from_dump` takes the size argument that is the buffer size. In other words it usually should be the same as the buffer size that you've passed to the `safe_dump_to`.  
  
Here' a non trivial example on that https://github.com/boostorg/stacktrace/blob/c63afe54a24da91bf0f0c879fbab8202accb44ea/example/terminate_handler.cpp#L173  
  
If you do wish to get the actually consumed bytes, you should `sizeof(boost::stacktrace::frame::native_frame_ptr_t) * frames`. I'll update the reference to reflect that.

---

## Comment 2

> Username: apolukhin  
> Created at: 2018-09-17 08:20:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/62#issuecomment-421924582  

Updated and regenerated the docs
