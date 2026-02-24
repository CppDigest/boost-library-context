# #128 Fix for the ticket #13155 - log doesn't build on a system with pthreads [Closed]

> Username: mkartashev  
> Created at: 2017-08-10 12:18:04 UTC  
> Updated at: 2017-08-16 06:17:34 UTC  
> Closed at: 2017-08-16 06:17:34 UTC  
> Url: https://github.com/boostorg/thread/pull/128  

Changed a call to getpagesize() that is not available since POSIX.1-2001 with  
the ::sysconf( _SC_PAGESIZE) call.  
  
The fix was tested by building with "b2 stdlib=sun=stlport" on  
- Oracle Linux 7.3,  
- Oracle Linux 6.9,  
- Solaris 11.3

---

## Comment 1

> Username: viboes  
> Created_at: 2017-08-10 21:46:43 UTC  
> Url: https://github.com/boostorg/thread/pull/128#issuecomment-321682388  

I would prefer a PR where the change is done conditionally as the documentation says  
  
       PAGESIZE - _SC_PAGESIZE  
              Size of a page in bytes.  Must not be less than 1.  (Some  
              systems use PAGE_SIZE instead.)  
  
and I don't know which systems are those.  
  
In addition sysconf return a long.

---

## Comment 2

> Username: viboes  
> Created_at: 2017-08-14 13:13:51 UTC  
> Url: https://github.com/boostorg/thread/pull/128#issuecomment-322186387  

I have tried with   
```  
#ifdef BOOST_THREAD_USES_GETPAGESIZE  
          std::size_t page_size = getpagesize();  
#else  
          std::size_t page_size = ::sysconf( _SC_PAGESIZE);  
#endif  
```  
  
in Macos and it works. I've commit it in develop and will see how it behaves with other platforms.

---

## Comment 3

> Username: mkartashev  
> Created_at: 2017-08-15 18:23:17 UTC  
> Updated_at: 2017-08-15 18:51:14 UTC  
> Url: https://github.com/boostorg/thread/pull/128#issuecomment-322547808  

Yes, I saw this patch, tested it on Solaris 11.3 and Oracle Linux (6 and 7), and can confirm that it works there. Thanks!

---

## Comment 4

> Username: viboes  
> Created_at: 2017-08-16 06:17:34 UTC  
> Url: https://github.com/boostorg/thread/pull/128#issuecomment-322676682  

Thanks for the information.  
I'll close this PR.

---
