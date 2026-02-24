# #330 Use boost::throw_exception rather than direct throw [Merged]

> Username: edtanous  
> Created at: 2023-07-11 15:14:48 UTC  
> Updated at: 2023-08-14 15:38:05 UTC  
> Merged at: 2023-08-14 09:41:37 UTC  
> Closed at: 2023-08-14 09:41:37 UTC  
> Url: https://github.com/boostorg/process/pull/330  

Using boost::throw_exception allows for modifications to these exceptions on a per-application basis, including overriding with custom implementations.  
  
This also has the benefit of allowing compilation with -fno-exceptions set, which should make this code more portable.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2023-07-12 01:44:24 UTC  
> Url: https://github.com/boostorg/process/pull/330#issuecomment-1631726754  

Can you add the boost::source_location too? I'll merge then.

---

## Comment 2

> Username: edtanous  
> Created_at: 2023-07-12 01:54:59 UTC  
> Url: https://github.com/boostorg/process/pull/330#issuecomment-1631733060  

> Can you add the boost::source_location too? I'll merge then.  
  
Do you mean as a #include, or would you like me to change each of these methods to use the overload of boost::throw_exception that accepts a source_location as an argument?

---

## Comment 3

> Username: edtanous  
> Created_at: 2023-07-14 17:59:40 UTC  
> Url: https://github.com/boostorg/process/pull/330#issuecomment-1636199031  

> Can you add the boost::source_location too? I'll merge then.  
  
I've made my best guess as to what I think you're asking for here.  Please take a look.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2023-07-15 02:49:09 UTC  
> Url: https://github.com/boostorg/process/pull/330#issuecomment-1636634332  

Looks good, although I'd also add it to the throw_last_error functions.

---

## Comment 5

> Username: edtanous  
> Created_at: 2023-07-17 17:58:40 UTC  
> Url: https://github.com/boostorg/process/pull/330#issuecomment-1638616390  

> Looks good, although I'd also add it to the throw_last_error functions.  
  
Done.

---

## Comment 6

> Username: edtanous  
> Created_at: 2023-08-14 15:38:05 UTC  
> Url: https://github.com/boostorg/process/pull/330#issuecomment-1677573608  

Thank you.  I appreciate you.

---
