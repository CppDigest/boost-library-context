# #124 - diagnostic stream to VisualStudio output window [Closed]

> Username: tobias-loew  
> Created at: 2024-02-27 15:42:01 UTC  
> Updated at: 2024-03-30 18:50:20 UTC  
> Closed at: 2024-03-30 18:50:20 UTC  
> Url: https://github.com/boostorg/parser/issues/124  

I'm not sure if this is too VS-centric, but for non-console applications it would be helpful having a predefined error-handler, that sends the trace-messages to the VS-Output-Window.  
Here's a link how to wrap a C++ stream around the OutputDebugString-API  
https://gist.github.com/comargo/f7fe244e539325cef0f66704b58dc35a

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-28 05:00:52 UTC  
> Url: https://github.com/boostorg/parser/issues/124#issuecomment-1968233202  

Yes, I'm afraid this is too Windows-centric for me.  That code is simple enough, but getting a `char *` or a `wchar_t *` to print using one of those Win32 syscalls is nontrivial in the general case.  However, if someone were to submit a PR for this as a new `error_handler` option, I would merge it. :)

---

## Comment 2

> Username: tobias-loew  
> Created at: 2024-03-01 14:48:10 UTC  
> Url: https://github.com/boostorg/parser/issues/124#issuecomment-1973332414  

Got the message :) Which branch against would you like the PR? develop?

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-03-02 02:28:06 UTC  
> Url: https://github.com/boostorg/parser/issues/124#issuecomment-1974208676  

Yes!  Develop would be great.  I'll re-open this, then.  Please mention it by number in the PR commit message.

---

## Comment 4

> Username: tobias-loew  
> Created at: 2024-03-03 07:44:21 UTC  
> Url: https://github.com/boostorg/parser/issues/124#issuecomment-1975075779  

It already works with the error_handler:  
![image](https://github.com/tzlaine/parser/assets/8749019/2f61c525-8bb0-4d4e-9490-c7a512c8b77a)

---

## Comment 5

> Username: tobias-loew  
> Created at: 2024-03-03 09:13:08 UTC  
> Updated at: 2024-03-03 09:13:48 UTC  
> Url: https://github.com/boostorg/parser/issues/124#issuecomment-1975096129  

Opened a PR https://github.com/tzlaine/parser/pull/156 for this.  
Now, I would also like to send the trace to the VS-output window. I've already changed the functions in printing.hpp to additionally take an ostream-argument.  
The question is: what to feed in for that arg from the parser?  
  
- take the stream from the error_handler (not really an option)  
- add arg to parser-api to specify ostream for trace (also not really good)  
- change the trace-argument from enum to a struct which also includes the ostream, default creatable from trace-enum with std::cout as stream (IMHO the best solution. I'm currently implementing this.)
