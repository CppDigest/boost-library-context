# #19 - How can I wrap iconv_t in boost::scope::unique_resource in just one line? [Closed]

> Username: liubing  
> Created at: 2024-03-12 01:14:24 UTC  
> Updated at: 2024-03-12 11:56:29 UTC  
> Closed at: 2024-03-12 09:06:49 UTC  
> Url: https://github.com/boostorg/scope/issues/19  

I try to do this:  
```  
using unique_iconv_t = boost::scope::unique_resource<iconv_t, boost::core::functor<iconv_close>, boost::scope::unallocated_resource<(iconv_t)(-1)>>;  
unique_iconv_t cd(iconv_open("WCHAR_T//IGNORE", "UTF8"));  
```  
But the compiler gives:  
```  
error: ‘-1’ is not a valid template argument for ‘void*’ because it is not the address of a variable  
```  
Must I define my own trait class to solve this? Is there a simpler way to write this?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-03-12 09:06:49 UTC  
> Url: https://github.com/boostorg/scope/issues/19#issuecomment-1991094319  

Yes, it looks like you have to define resource traits in this case.

---

## Comment 2

> Username: Lastique  
> Created at: 2024-03-12 09:16:14 UTC  
> Url: https://github.com/boostorg/scope/issues/19#issuecomment-1991120801  

In C++20 you could work around it with something like this:  
  
```  
struct invalid_iconv  
{  
    operator iconv_t() const noexcept  
    {  
        return (iconv_t)(-1);  
    }  
};  
  
using unique_iconv_t = boost::scope::unique_resource<iconv_t, boost::core::functor<iconv_close>, boost::scope::unallocated_resource<invalid_iconv{}>>;  
```  
  
but this is a hack and is not much shorter code than the proper resource traits.

---

## Comment 3

> Username: liubing  
> Created at: 2024-03-12 10:30:42 UTC  
> Url: https://github.com/boostorg/scope/issues/19#issuecomment-1991312898  

Thank you for the explanation. What puzzles me is that `iconv_t` is actually `void*`, and a Windows `HANDLE` is also a `void*`, and `INVALID_HANDLE_VALUE` is defined as:  
```  
#define INVALID_HANDLE_VALUE ((HANDLE)(LONG_PTR)-1)  
```  
It looks exactly the same as `(iconv_t)(-1)`. So why is this working?  
```  
using unique_handle = boost::scope::unique_resource<  
    HANDLE,  
    boost::core::functor< CloseHandle >,  
    boost::scope::unallocated_resource< INVALID_HANDLE_VALUE, (HANDLE)NULL >  
>;  
```  
Could it be that MSVC is more permissive than GCC?

---

## Comment 4

> Username: Lastique  
> Created at: 2024-03-12 11:12:58 UTC  
> Url: https://github.com/boostorg/scope/issues/19#issuecomment-1991402700  

> Could it be that MSVC is more permissive than GCC?  
  
Apparently so. You can see in this [CI run](https://github.com/boostorg/scope/actions/runs/8247694678/job/22556418946) that MSVC succeeded but clang and gcc on Windows failed to compile this code.  
  
Looks like I should remove the `INVALID_HANDLE_VALUE` example from the docs, or at least note that this code only works on MSVC.

---

## Comment 5

> Username: liubing  
> Created at: 2024-03-12 11:56:27 UTC  
> Url: https://github.com/boostorg/scope/issues/19#issuecomment-1991483583  

Got it, thank you.
