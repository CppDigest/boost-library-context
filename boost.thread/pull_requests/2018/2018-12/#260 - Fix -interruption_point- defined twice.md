# #260 Fix "interruption_point" defined twice [Merged]

> Username: DesWurstes  
> Created at: 2018-12-31 09:31:32 UTC  
> Updated at: 2019-01-20 11:31:59 UTC  
> Merged at: 2019-01-03 18:02:57 UTC  
> Closed at: 2019-01-03 18:02:57 UTC  
> Url: https://github.com/boostorg/thread/pull/260  

```  
In file included from /usr/local/include/boost/thread/thread_only.hpp:22,  
                 from /usr/local/include/boost/thread/thread.hpp:12,  
                 from /usr/local/include/boost/thread.hpp:13,  
                 from validation.cpp:48:  
/usr/local/include/boost/thread/detail/thread.hpp:591:32: warning: redundant redeclaration of 'void boost::this_thread::interruption_point()' in same scope [-Wredundant-decls]  
         void BOOST_THREAD_DECL interruption_point();  
                                ^~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/thread/condition_variable.hpp:16,  
                 from ./checkqueue.h:13,  
                 from validation.cpp:12:  
/usr/local/include/boost/thread/pthread/condition_variable.hpp:32:32: note: previous declaration of 'void boost::this_thread::interruption_point()'  
         void BOOST_THREAD_DECL interruption_point();  
                                ^~~~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: viboes  
> Created_at: 2018-12-31 10:04:29 UTC  
> Url: https://github.com/boostorg/thread/pull/260#issuecomment-450627749  

I can not accept this PR.  
If the declaration must be only once, then we should avoid having it in two files.   
I would accept a PR where the declaration is in another file included by both :)

---

## Comment 2

> Username: DesWurstes  
> Created_at: 2018-12-31 10:40:58 UTC  
> Url: https://github.com/boostorg/thread/pull/260#issuecomment-450631283  

Thanks for the quick reply! Is it okay now?

---

## Review 3 [Commented]

> Username: viboes  
> Created_at: 2018-12-31 16:51:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/260#pullrequestreview-188516229  

📁 include/boost/thread/detail/thread.hpp

```diff
 592 | 
 593 | #if defined BOOST_THREAD_PROVIDES_INTERRUPTIONS
 591 |-         void BOOST_THREAD_DECL interruption_point();
```

> Username: viboes  
> Created_at: 2018-12-31 16:51:25 UTC  
> Updated_at: 2019-01-01 09:49:46 UTC  
> Url: https://github.com/boostorg/thread/pull/260#discussion_r244601692  

Wondering if the file shouldn't contains the declarations related to interruptions :)

> Username: viboes  
> Created_at: 2018-12-31 16:51:49 UTC  
> Updated_at: 2019-01-01 09:49:46 UTC  
> Url: https://github.com/boostorg/thread/pull/260#discussion_r244601719  

In this case the file name should be interrupt.hpp


---

## Comment 4

> Username: viboes  
> Created_at: 2018-12-31 16:53:01 UTC  
> Url: https://github.com/boostorg/thread/pull/260#issuecomment-450668025  

Thanks fro the quick reaction.   
  
Please let me know what do you think about moving all the interrupt related declarations declarations to a single interrupt.hpp file

---

## Comment 5

> Username: DesWurstes  
> Created_at: 2018-12-31 20:10:55 UTC  
> Url: https://github.com/boostorg/thread/pull/260#issuecomment-450686427  

Is that okay now?

---

## Comment 6

> Username: viboes  
> Created_at: 2019-01-03 18:02:38 UTC  
> Url: https://github.com/boostorg/thread/pull/260#issuecomment-451226195  

Yes, it is ok now :)

---
