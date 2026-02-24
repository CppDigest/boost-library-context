# #4 Asio cray support [Closed]

> Username: rsdale  
> Created at: 2014-05-13 20:21:44 UTC  
> Updated at: 2014-10-16 09:09:57 UTC  
> Closed at: 2014-10-16 09:09:57 UTC  
> Url: https://github.com/boostorg/asio/pull/4  

This update allows asio to work with the Cray C++ compiler.    Cray C++ defines **GNUC** and for the most part implements the gnu extensions to C++.  What Cray C++ does NOT do is support inline assembly code.  This update addresses this shortcoming of Cray C++ in asio.  
  
cray_fenced_block.hpp is based on gcc_x86_fenced_block.hpp, but uses the __builtin_ia32_sfence and __builtin_ia32_lfence intrinsics, to avoid use of inline assembly in the implementation of the cray_fenced_block class.  
  
fenced_block.hpp is modified to call cray_fenced_block.hpp when _CRAYC is asserted.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2014-05-14 23:09:37 UTC  
> Url: https://github.com/boostorg/asio/pull/4#issuecomment-43149408  

These functions are gcc intrinsics as well aren't they? If so, rather than adding a new file I will change gcc_x86_fenced_block to use the intrinsics if they are available, in preference to the inline assembly.

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2014-05-14 23:11:16 UTC  
> Url: https://github.com/boostorg/asio/pull/4#issuecomment-43149522  

What version number does cray c++ use for `__GNUC__` ?

---

## Comment 3

> Username: rsdale  
> Created_at: 2014-05-15 12:36:47 UTC  
> Url: https://github.com/boostorg/asio/pull/4#issuecomment-43203582  

On 05/14/2014 06:11 PM, chriskohlhoff wrote:  
  
> What version number does cray c++ use for |**GNUC**| ?  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/asio/pull/4#issuecomment-43149522.  
  
4

---

## Comment 4

> Username: rsdale  
> Created_at: 2014-05-15 12:59:29 UTC  
> Url: https://github.com/boostorg/asio/pull/4#issuecomment-43205725  

On 05/14/2014 06:09 PM, chriskohlhoff wrote:  
  
> These functions are gcc intrinsics as well aren't they? If so, rather   
> than adding a new file I will change gcc_x86_fenced_block to use the   
> intrinsics if they are available, in preference to the inline assembly.  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/asio/pull/4#issuecomment-43149408.  
>   
> That works for me!  
  
Thanks for your help,  
Richard

---

## Comment 5

> Username: chriskohlhoff  
> Created_at: 2014-05-16 00:11:46 UTC  
> Url: https://github.com/boostorg/asio/pull/4#issuecomment-43280752  

Does it also define `__SSE2__`?

---

## Comment 6

> Username: rsdale  
> Created_at: 2014-05-16 12:21:45 UTC  
> Url: https://github.com/boostorg/asio/pull/4#issuecomment-43325020  

On 05/15/2014 07:11 PM, chriskohlhoff wrote:  
  
> Does it also define |**SSE2**|?  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/asio/pull/4#issuecomment-43280752.  
>   
> Yes,  **SSE2** == 1.  
  
regards,  
rsd

---
