# #255 - Most of the platform defintions in include/boost/thread/detail/platform.hpp are unused [Open]

> Username: jeking3  
> Created at: 2018-11-24 13:14:41 UTC  
> Updated at: 2019-05-09 19:28:45 UTC  
> Url: https://github.com/boostorg/thread/issues/255  

Perhaps it would be a good idea to remove the unused definitions and switch to using Boost.Predef for detection where needed instead?  
  
Example:  
```  
jking@ubuntu:~/boost/libs/thread$ grep -r AMIGAOS .  
./include/boost/thread/detail/platform.hpp:#  define BOOST_THREAD_AMIGAOS  
jking@ubuntu:~/boost/libs/thread$ grep -r THREAD_SOL .  
./include/boost/thread/detail/platform.hpp:#  define BOOST_THREAD_SOLARIS  
jking@ubuntu:~/boost/libs/thread$ grep -r THREAD_LINUX .  
./include/boost/thread/detail/platform.hpp:#  define BOOST_THREAD_LINUX  
jking@ubuntu:~/boost/libs/thread$ grep -r THREAD_BSD .  
./include/boost/thread/detail/platform.hpp:#  define BOOST_THREAD_BSD  
```

---

## Comment 1

> Username: viboes  
> Created at: 2018-11-24 16:01:43 UTC  
> Url: https://github.com/boostorg/thread/issues/255#issuecomment-441377472  

why not
