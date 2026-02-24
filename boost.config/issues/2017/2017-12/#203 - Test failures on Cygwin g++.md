# #203 - Test failures on Cygwin g++ [Closed]

> Username: pdimov  
> Created at: 2017-12-22 23:23:43 UTC  
> Updated at: 2018-01-30 10:00:51 UTC  
> Closed at: 2018-01-30 10:00:51 UTC  
> Url: https://github.com/boostorg/config/issues/203  

cstdint_test.cpp:  
  
```  
test\cstdint_test.cpp(119): test 'sizeof(T1) == sizeof(T2)' failed in function '  
void integral_constant_type_check(T1, T2) [with T1 = signed char; T2 = int]'  
test\cstdint_test.cpp(119): test 'sizeof(T1) == sizeof(T2)' failed in function '  
void integral_constant_type_check(T1, T2) [with T1 = unsigned char; T2 = int]'  
test\cstdint_test.cpp(120): test 't1 == t2' failed in function 'void integral_co  
nstant_type_check(T1, T2) [with T1 = unsigned char; T2 = int]'  
test\cstdint_test.cpp(136): test 't2 > 0' failed in function 'void integral_cons  
tant_type_check(T1, T2) [with T1 = unsigned char; T2 = int]'  
test\cstdint_test.cpp(119): test 'sizeof(T1) == sizeof(T2)' failed in function '  
void integral_constant_type_check(T1, T2) [with T1 = short int; T2 = int]'  
test\cstdint_test.cpp(119): test 'sizeof(T1) == sizeof(T2)' failed in function '  
void integral_constant_type_check(T1, T2) [with T1 = short unsigned int; T2 = in  
t]'  
test\cstdint_test.cpp(120): test 't1 == t2' failed in function 'void integral_co  
nstant_type_check(T1, T2) [with T1 = short unsigned int; T2 = int]'  
test\cstdint_test.cpp(136): test 't2 > 0' failed in function 'void integral_cons  
tant_type_check(T1, T2) [with T1 = short unsigned int; T2 = int]'  
OK  
8 errors detected.  
```  
  
and config_test.cpp:  
  
```  
In file included from test\boost_no_cxx14_hdr_shared_mutex.ipp:13:0,  
                 from test\config_test.cpp:306:  
/usr/lib/gcc/i686-pc-cygwin/6.4.0/include/c++/shared_mutex: In member function '  
void std::__shared_mutex_pthread::lock()':  
/usr/lib/gcc/i686-pc-cygwin/6.4.0/include/c++/shared_mutex:105:51: error: 'pthre  
ad_rwlock_wrlock' was not declared in this scope  
       int __ret = pthread_rwlock_wrlock(&_M_rwlock);  
                                                   ^  
/usr/lib/gcc/i686-pc-cygwin/6.4.0/include/c++/shared_mutex: In member function '  
bool std::__shared_mutex_pthread::try_lock()':  
/usr/lib/gcc/i686-pc-cygwin/6.4.0/include/c++/shared_mutex:115:54: error: 'pthre  
ad_rwlock_trywrlock' was not declared in this scope  
       int __ret = pthread_rwlock_trywrlock(&_M_rwlock);  
                                                      ^  
/usr/lib/gcc/i686-pc-cygwin/6.4.0/include/c++/shared_mutex: In member function '  
void std::__shared_mutex_pthread::unlock()':  
/usr/lib/gcc/i686-pc-cygwin/6.4.0/include/c++/shared_mutex:125:77: error: 'pthre  
ad_rwlock_unlock' was not declared in this scope  
       int __ret __attribute((__unused__)) = pthread_rwlock_unlock(&_M_rwlock);  
                                                                             ^  
/usr/lib/gcc/i686-pc-cygwin/6.4.0/include/c++/shared_mutex: In member function '  
void std::__shared_mutex_pthread::lock_shared()':  
/usr/lib/gcc/i686-pc-cygwin/6.4.0/include/c++/shared_mutex:141:42: error: 'pthre  
ad_rwlock_rdlock' was not declared in this scope  
  __ret = pthread_rwlock_rdlock(&_M_rwlock);  
                                          ^  
/usr/lib/gcc/i686-pc-cygwin/6.4.0/include/c++/shared_mutex: In member function '  
bool std::__shared_mutex_pthread::try_lock_shared()':  
/usr/lib/gcc/i686-pc-cygwin/6.4.0/include/c++/shared_mutex:152:54: error: 'pthre  
ad_rwlock_tryrdlock' was not declared in this scope  
       int __ret = pthread_rwlock_tryrdlock(&_M_rwlock);  
                                                      ^  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-01-30 10:00:51 UTC  
> Url: https://github.com/boostorg/config/issues/203#issuecomment-361541562  

Fixed in https://github.com/boostorg/config/commit/d0b3e9d59e79016d3650a3a4b304121e5b80e229 (the remaining appveyor.yml issues are still a work in progress).
