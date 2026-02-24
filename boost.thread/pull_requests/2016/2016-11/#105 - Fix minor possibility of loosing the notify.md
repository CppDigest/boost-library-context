# #105 Fix minor possibility of loosing the notify [Merged]

> Username: apolukhin  
> Created at: 2016-11-02 20:15:56 UTC  
> Updated at: 2016-11-03 07:09:51 UTC  
> Merged at: 2016-11-03 07:06:34 UTC  
> Closed at: 2016-11-03 07:06:34 UTC  
> Url: https://github.com/boostorg/thread/pull/105  

Notification can be send during pthread_cond_wait wakeup from EINTR and the `while (res == EINTR)` will ignore the signal without rechecking the condition:  
  
```  
Thread 1                                    Thread 2  
---------------------------------------------------------------------------------------  
m.lock()  
flag=false  
m.unlock()  
                                            m.lock()  
                                            while(!flag)  
                                            condition_variable::wait(m)  
                                            res = pthread_cond_wait(&cond,the_mutex); // waits  
m.lock()                                    // waits  
flag=true                                   // waits  
m.unlock()                                  // waits  
                                            res = pthread_cond_wait(&cond,the_mutex); // # wakes up, does not locks the the_mutex yet  
condition_variable::notify_one() // lost  
                                            res = pthread_cond_wait(&cond,the_mutex); // #locks the the_mutex, returnes EINTR  
                                            } while (res == EINTR);  
                                              
                                            res = pthread_cond_wait(&cond,the_mutex); // waits for infinity  
```

---

## Comment 1

> Username: viboes  
> Created_at: 2016-11-02 20:40:28 UTC  
> Url: https://github.com/boostorg/thread/pull/105#issuecomment-257992047  

You are surely right that it could be blocked if there is a EINTR in this case.  
  
I don't remember why the code protects from EINTR as it seems that pthread_cond_wait never should return EINTR.  
  
http://pubs.opengroup.org/onlinepubs/007908775/xsh/pthread_cond_wait.html  
  
Have you find that it return EINTR? If yes on which system?  
  
I would merge it if you have a case that blocks (at least from time to time).

---

## Comment 2

> Username: apolukhin  
> Created_at: 2016-11-03 06:18:43 UTC  
> Url: https://github.com/boostorg/thread/pull/105#issuecomment-258071157  

> I don't remember why the code protects from EINTR as it seems that pthread_cond_wait never should return EINTR.  
  
EINTR issue was reported in here https://svn.boost.org/trac/boost/ticket/6200  
  
I'm not sure on what platform does it block, because it was blocking on a remote host that compiles the source code and runs the binary (http://coliru.stacked-crooked.com/compile). This could be also an issue of the sandbox/container that runs the code.  
  
`lsb_release -a; uname -a` on that remote currently produces the following:  
  
```  
No LSB modules are available.  
Distributor ID: Debian  
Description:    Debian GNU/Linux  
Release:    n/a  
Codename:   n/a  
Linux stacked-crooked 3.2.0-74-virtual #109-Ubuntu SMP Tue Dec 9 17:04:48 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux  
```  
  
But I have no guarantee that it is the correct data and that it was blocking on that system (there may be more than one container, and those containers may have different OS/libc).  
  
Code that was blocking is pretty simple: https://github.com/apolukhin/Boost-Cookbook-4880OS/blob/47f7d5ed98541f6a336a2bb5cd19070bc79a93bb/Chapter05/work_queue/main.cpp  
  
Adding a `flush()` method fixes the blocks: https://github.com/apolukhin/Boost-Cookbook-4880OS/blob/23f7bb8623e42eeee2c8683042e7f291ebda38f0/Chapter05/work_queue/main.cpp

---

## Comment 3

> Username: viboes  
> Created_at: 2016-11-03 07:09:41 UTC  
> Updated_at: 2016-11-03 07:09:51 UTC  
> Url: https://github.com/boostorg/thread/pull/105#issuecomment-258075929  

I've merged the PR. We will see.  
  
Thanks Anthony.

---
