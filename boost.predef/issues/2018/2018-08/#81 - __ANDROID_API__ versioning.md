# #81 - __ANDROID_API__ versioning? [Closed]

> Username: jeking3  
> Created at: 2018-08-15 17:55:52 UTC  
> Updated at: 2019-01-10 19:29:14 UTC  
> Closed at: 2019-01-10 19:29:14 UTC  
> Url: https://github.com/boostorg/predef/issues/81  

Is there support in predef to handle conditionals around this android macro today?  
```  
#if __ANDROID_API__ >= 28  
int getentropy(void* __buffer, size_t __buffer_size) __wur __INTRODUCED_IN(28);  
  
ssize_t getrandom(void* __buffer, size_t __buffer_size, unsigned int __flags) __wur __INTRODUCED_IN(28);  
#endif /* __ANDROID_API__ >= 28 */  
```  
  
See: https://github.com/boostorg/uuid/issues/76

---

## Comment 1

> Username: Lastique  
> Created at: 2018-08-15 20:31:57 UTC  
> Url: https://github.com/boostorg/predef/issues/81#issuecomment-413326275  

Actually, that Boost.UUID bug indicates that `BOOST_OS_LINUX` gets defined to non-zero on Android. Is this the intended behavior?

---

## Comment 2

> Username: grafikrobot  
> Created at: 2018-08-15 20:40:35 UTC  
> Url: https://github.com/boostorg/predef/issues/81#issuecomment-413328767  

@Lastique somewhat intended.. As Android is technically a variant of Linux. Practically just about any Linux related code will also compile on Android. Concretely, the compilers are defining one of the linux predef symbols used for detection, and Boost Predef allows the overlapping definition in this case.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-08-15 20:41:38 UTC  
> Url: https://github.com/boostorg/predef/issues/81#issuecomment-413329061  

Actually, I suspect that the problem is that Boost.Predef defines its macros differently depending on the order of its headers inclusion. Each header in `boost/predef/os` checks if `BOOST_PREDEF_DETAIL_OS_DETECTED` is defined and defines it if the corresponding OS is detected. Thus if the user includes `boost/predef/os/linux.h` first, `BOOST_OS_LINUX` is defined. If he includes `boost/predef/os/android.h` first then `BOOST_OS_ANDROID` is defined. IMHO, this is a serious problem.

---

## Comment 4

> Username: grafikrobot  
> Created at: 2018-08-15 20:41:56 UTC  
> Url: https://github.com/boostorg/predef/issues/81#issuecomment-413329166  

Actually scratch what I said.. They aren't supposed to be overlapping.

---

## Comment 5

> Username: grafikrobot  
> Created at: 2018-08-15 20:42:58 UTC  
> Url: https://github.com/boostorg/predef/issues/81#issuecomment-413329451  

@Lastique yes, I agree, now that I've read the code again. There's other places where this comes up (and has an appropriate fix).

---

## Comment 6

> Username: Lastique  
> Created at: 2018-08-15 20:46:38 UTC  
> Url: https://github.com/boostorg/predef/issues/81#issuecomment-413330589  

See also https://github.com/boostorg/predef/issues/41.  
  
So, would you like a separate ticket about the order of inclusion thing?

---

## Comment 7

> Username: grafikrobot  
> Created at: 2018-08-15 20:51:52 UTC  
> Url: https://github.com/boostorg/predef/issues/81#issuecomment-413332274  

@Lastique I think #41 is enough. Just haven't had time to deal with it.. Or think about it :-)

---

## Comment 8

> Username: grafikrobot  
> Created at: 2019-01-10 19:29:14 UTC  
> Url: https://github.com/boostorg/predef/issues/81#issuecomment-453223108  

Fixed by https://github.com/boostorg/predef/commit/a340a52605a01983a59e6fcb09669fe54b96c684
