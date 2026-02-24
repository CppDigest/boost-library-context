# #495 - On Windows passing an empty argument to a subprocess throws an exception in the conversion from UTF-8 to UTF-16 [Closed]

> Username: ThirtyNineWinks  
> Created at: 2025-06-13 16:06:33 UTC  
> Updated at: 2025-06-21 02:33:28 UTC  
> Closed at: 2025-06-20 16:37:09 UTC  
> Url: https://github.com/boostorg/process/issues/495  

in process\v2\detail\utf8.hpp `conv_string` calls `size_as_wide` to find out how much space to reserve for the output of the transcoding. In process\src\detail\utf8.cpp, the `size_as_wide` function uses Windows `MultiByteToWideChar` to find exactly the right number of bytes needed. This function returns zero on error, and handles and throws an exception:  
  
> if (res == 0u)  
>    handle_error(ec);  
  
In the case of an empty string, a zero length is not an error.  
  
This prevents explicitly passing empty arguments, like `echo ""`. (Of course, you don't need to do that, but there are other cases where you might want to pass an empty argument.)  
  
Can you fix `conv_string` to check for an empty input? Or fix `size_as_wide` to recognize this "error" as a special case and just return zero?  
  
(This is in version 1.88)

---

## Comment 1

> Username: ThirtyNineWinks  
> Created at: 2025-06-20 16:49:34 UTC  
> Url: https://github.com/boostorg/process/issues/495#issuecomment-2992244830  

Perhaps if size is zero, `convert_to_wide` should write a zero into `out[0]`?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2025-06-21 02:33:27 UTC  
> Url: https://github.com/boostorg/process/issues/495#issuecomment-2993262851  

That would be handles by the `std::string` in which the value gets stored. And the way I am using MultiByteToWideChar does not include a null terminator either.
