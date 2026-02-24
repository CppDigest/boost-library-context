# #131 Improve interoperability with std::system_error [Closed]

> Username: apolukhin  
> Created at: 2024-12-16 07:36:51 UTC  
> Updated at: 2024-12-16 17:42:11 UTC  
> Closed at: 2024-12-16 17:42:11 UTC  
> Url: https://github.com/boostorg/system/pull/131  

While migrating Boost.DLL to std::error_code https://github.com/boostorg/dll/pull/76 I've noticed that boost::system_error is not related to std::system_error. Because of that, users have to duplicate the catch blocks if there's a need to work with error code:  
```cpp  
try {  
  some_throwing_function();  
} catch (const std::system_error& err) {  
  process_error_code(err.code());  
} catch (const boost::system::system_error& err) {  
  process_error_code(err.code());  
}  
```  
  
With this PR the seconds catch block could be eliminated. Is there an interest in such functionality? The output of e.what() changes, is that OK?

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-12-16 09:33:44 UTC  
> Url: https://github.com/boostorg/system/pull/131#issuecomment-2545060461  

No, it's not OK, because it throws away information, namely the source location associated with the error code. E.g. https://godbolt.org/z/Tcxf8TdY9.  
  
I assume you don't care about this because you never added source locations to your error codes, but others have. :-)  
  
This will also change the behavior of code like the one you show - the first catch block will start catching our system_error and the second one will never be reached.

---
