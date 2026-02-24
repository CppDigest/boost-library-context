# #142 - Documentation mentions "OpenCL C API" [Closed]

> Username: leemes  
> Created at: 2014-06-18 08:42:37 UTC  
> Updated at: 2014-06-19 01:53:11 UTC  
> Closed at: 2014-06-19 01:53:11 UTC  
> Url: https://github.com/boostorg/compute/issues/142  

Not sure if this should be an _issue_, but github doesn't provide any private message mechanism so I need to post it here:  
  
There is no such thing as an "OpenCL C API". There is OpenCL C (= the language of the device code) and the OpenCL API (= the functions in the host program). The documentation and readme often mentions the OpenCL C API when referring to the latter, which is wrong. You should remove this tiny little "C" in the documentations to avoid confusion with the device code language.

---

## Comment 1

> Username: ghost  
> Created at: 2014-06-18 08:52:23 UTC  
> Url: https://github.com/boostorg/compute/issues/142#issuecomment-46410770  

surely this is to differentiate the C api from the C++ api that OpenCL provides?

---

## Comment 2

> Username: leemes  
> Created at: 2014-06-18 09:08:13 UTC  
> Updated at: 2014-06-18 09:41:30 UTC  
> Url: https://github.com/boostorg/compute/issues/142#issuecomment-46412111  

What you mean is officially called "C++ bindings" (unofficially sometimes also "C++ wrapper"). So we have three things: OpenCL C, the OpenCL API and OpenCL C++ bindings.   
  
If we say "OpenCL C API" this could mean the set of functions provided for the host program in C, or it could mean the set of functions provided for the device OpenCL C language (that's technically also an API). Now we need to differentiate them.  
  
Therefore, I'd stick to the names which Khronos uses (see also http://www.khronos.org/registry/cl/) to avoid confusion.

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-06-19 01:53:10 UTC  
> Url: https://github.com/boostorg/compute/issues/142#issuecomment-46515699  

Thanks for raising the issue! I've updated the references in the documentation from "OpenCL C API" to just "OpenCL API". See the commit here: b670212ee8dc0a4cdd2fcffa90f423ee43221aad.  
  
And GitHub issues work great for this sort of thing. But, just FYI,  you can also discuss Boost.Compute on the mailing list (address in the README file) and/or through private email with me (address in my profile and git logs).
