# #283 - can't build on solaris 11.3 using gcc 8.3.0 [Closed]

> Username: ohhmm  
> Created at: 2019-05-17 07:01:12 UTC  
> Updated at: 2022-01-19 06:30:18 UTC  
> Closed at: 2020-03-01 07:48:20 UTC  
> Url: https://github.com/boostorg/thread/issues/283  

https://github.com/boostorg/boost/issues/291  
  
https://github.com/boostorg/boost/files/3190241/log.txt  
  
./boost/thread/pthread/thread_data.hpp:60:5: error: missing binary operator before token "_sysconf"

---

## Comment 1

> Username: viboes  
> Created at: 2019-05-31 12:57:27 UTC  
> Url: https://github.com/boostorg/thread/issues/283#issuecomment-497698992  

Please, could you check why do we have this issue  
  
```  
./boost/thread/pthread/thread_data.hpp:60:5: error: missing binary operator before token "_sysconf"  
 #if PTHREAD_STACK_MIN > 0  
```  
  
is PTHREAD_STACK_MIN defined as _sysconf on this platform?  
  
Could you provide the preprocessed text?

---

## Comment 2

> Username: viboes  
> Created at: 2019-05-31 12:58:44 UTC  
> Url: https://github.com/boostorg/thread/issues/283#issuecomment-497699327  

Any PR with a reasonable solution is welcome. I will not do anything from my side as I don't have access to this platform.

---

## Comment 3

> Username: johnparke  
> Created at: 2019-06-28 17:03:00 UTC  
> Url: https://github.com/boostorg/thread/issues/283#issuecomment-506806952  

I had the same problem on SunOS 5.10, gcc version 7.4.0.  
  
The problem is caused by the statement following the sysconf reference, the #if statement:  
  
#ifdef BOOST_THREAD_USES_GETPAGESIZE  
          std::size_t page_size = getpagesize();  
#else  
          std::size_t page_size = ::sysconf( _SC_PAGESIZE);  
#endif  
#if PTHREAD_STACK_MIN > 0  
          if (size<PTHREAD_STACK_MIN) size=PTHREAD_STACK_MIN;  
#endif  
  
In the Solaris limits.h file, PTHREAD_STACK_MIN is defined as:  
  
`#define	PTHREAD_STACK_MIN	((size_t)_sysconf(_SC_THREAD_STACK_MIN))`  
  
So the if statement creates the error condition. I think what is expected is a value such as 16384.

---

## Comment 4

> Username: datalogics-robb  
> Created at: 2019-11-20 17:27:45 UTC  
> Url: https://github.com/boostorg/thread/issues/283#issuecomment-556137845  

This issue was introduced with this commit:  
  
https://github.com/boostorg/thread/commit/e54f7e39606b807b8f9fc84fbd58cafeabea6562  
  
The comment says that it was to fix a warning "comparing unsigned to zero" so I'm sure this would be safe to revert.  
  
https://github.com/boostorg/thread/pull/297

---

## Comment 5

> Username: maflcko  
> Created at: 2020-02-29 19:38:49 UTC  
> Url: https://github.com/boostorg/thread/issues/283#issuecomment-592986581  

The issue is fixed and can be closed?

---

## Comment 6

> Username: seerviashish  
> Created at: 2022-01-01 12:02:32 UTC  
> Url: https://github.com/boostorg/thread/issues/283#issuecomment-1003548201  

Still facing this issue. Is the issue closed? Ubuntu 20.04 and g++ --version = 10.3.0

---

## Comment 7

> Username: johnparke  
> Created at: 2022-01-03 12:18:19 UTC  
> Url: https://github.com/boostorg/thread/issues/283#issuecomment-1004054521  

Not using Solaris anymore, so don’t need to build on that platform.  
  
   
  
Thanks,  
  
   
  
John  
  
   
  
From: Ashish Chaudhary ***@***.***>   
Sent: Saturday, January 1, 2022 7:03 AM  
To: boostorg/thread ***@***.***>  
Cc: johnparke ***@***.***>; Comment ***@***.***>  
Subject: Re: [boostorg/thread] can't build on solaris 11.3 using gcc 8.3.0 (#283)  
  
   
  
Still facing this issue. Is the issue closed? Ubuntu 20.04 and g++ --version = 10.3.0  
  
—  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/thread/issues/283#issuecomment-1003548201> , or unsubscribe <https://github.com/notifications/unsubscribe-auth/AHJUWFQOFRYEKNFOHSKPP4TUT3UOHANCNFSM4HNSTQIQ> .  
Triage notifications on the go with GitHub Mobile for iOS <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  or Android <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub> .   
You are receiving this because you commented.  <https://github.com/notifications/beacon/AHJUWFQE7FC43RQDKWYYVYDUT3UOHA5CNFSM4HNSTQI2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOHPIO4KI.gif> Message ID: ***@***.*** ***@***.***> >

---

## Comment 8

> Username: hilga007  
> Created at: 2022-01-19 06:30:18 UTC  
> Url: https://github.com/boostorg/thread/issues/283#issuecomment-1016126934  

> Still facing this issue. Is the issue closed? Ubuntu 20.04 and g++ --version = 10.3.0  
  
I'm facing this issue as well in Ubuntu 22.04 with g++ --version=11.2.0  
  
I tried adding this to my CMakeLists.txt (did not work)  
  
SET(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} )  
SET(CMAKE_CXX_FLAGS " -pthread")
