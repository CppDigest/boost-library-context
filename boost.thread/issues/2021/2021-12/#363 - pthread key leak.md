# #363 - pthread key leak [Open]

> Username: Mario-Klebsch  
> Created at: 2021-12-02 08:27:57 UTC  
> Updated at: 2021-12-02 20:53:17 UTC  
> Url: https://github.com/boostorg/thread/issues/363  

I am using boost::thread statically linkes in a shared library and found a pthread key leak. When I repeat loading and unloading the library, pthread keys are leaking and after some time, I get   
  
main: boost_1_77_0/libs/thread/src/pthread/thread.cpp:148: void boost::detail::{anonymous}::create_current_thread_tls_key(): Assertion `!pthread_key_create(&current_thread_tls_key,&tls_destructor)' failed.  
  
The leaking pthread key is allocated in libs/thread/src/pthread/thread.cpp. The code for de-allocating the pthread key is #ifdef'ed with BOOST_THREAD_PATCH. Without  BOOST_THREAD_PATCH, the binary only calls to pthread_key_create, but no call to pthread_key_delete:  
  
The problem seems to be solved, if I #define BOOST_THREAD_PATCH. But this #ifdef was added in 47357de276fe4fc01469f34c1dbf8b26fdbc1c4b to solve bug #12049. (https://www.boost.org/doc/libs/1_64_0/doc/html/thread/changes.html)  (https://svn.boost.org/trac10/ticket/12049)  
  
Unfortunately, I do not really understand the problem in ticket #12049. To me, it looks like a race condition and the leaking pthread key just prevents any harm caused by the race condition. That doesn't really look like a fix to me, just solves one problem by creating a new one. :-(  
  
Here is my source code: [boost_pthread_key_leak.zip](https://github.com/boostorg/thread/files/7640118/boost_pthread_key_leak.zip)  
  
I am using linux on x86_64, c++ (Gentoo 10.3.0-r2 p3) 10.3.0
