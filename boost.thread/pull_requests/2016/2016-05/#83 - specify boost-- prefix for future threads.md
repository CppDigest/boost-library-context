# #83 specify boost:: prefix for future threads [Merged]

> Username: v4hn  
> Created at: 2016-05-09 19:13:12 UTC  
> Updated at: 2016-05-26 05:39:30 UTC  
> Merged at: 2016-05-26 05:39:30 UTC  
> Closed at: 2016-05-26 05:39:30 UTC  
> Url: https://github.com/boostorg/thread/pull/83  

if either thread/detail/force_cast.hpp or thread/detail/singleton.hpp is  
included before thread/future.hpp, the "thread" symbol resolves to the  
boost::detail::thread _namespace_ instead of the boost::thread class.  
  
Also it doesn't hurt to have the ns-prefixes in here.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-05-09 19:30:57 UTC  
> Url: https://github.com/boostorg/thread/pull/83#issuecomment-217964920  

Hi,  
  
sorry, but what are those files thread/detail/force_cast.hpp or thread/detail/singleton.hpp?

---

## Comment 2

> Username: v4hn  
> Created_at: 2016-05-09 19:39:05 UTC  
> Url: https://github.com/boostorg/thread/pull/83#issuecomment-217966878  

On Mon, May 09, 2016 at 12:30:58PM -0700, Vicente J. Botet Escriba wrote:  
  
> sorry, but what are those files thread/detail/force_cast.hpp or thread/detail/singleton.hpp?  
  
What do you mean? These are detail headers provided by this boost-module:  
  
https://github.com/boostorg/thread/blob/develop/include/boost/thread/detail/force_cast.hpp  
and   
https://github.com/boostorg/thread/blob/develop/include/boost/thread/detail/singleton.hpp  
  
An alternative solution would be to change the namespaces in these files to thread_detail  
instead, but that would break existing code.

---

## Comment 3

> Username: v4hn  
> Created_at: 2016-05-25 17:46:45 UTC  
> Url: https://github.com/boostorg/thread/pull/83#issuecomment-221652579  

Is there anything wrong with the patch?  
Anything I can do to get this merged?

---

## Comment 4

> Username: viboes  
> Created_at: 2016-05-25 21:13:11 UTC  
> Url: https://github.com/boostorg/thread/pull/83#issuecomment-221709341  

Sorry, I missed your post some weeks ago.  
  
I was not aware of those files, and I don't know why I didn't found them.   
  
Anyway, these are files that are not included by any file in boost/thread isn't it?  
  
```  
new-host:include viboes$ grep -r force_cast *  
boost/thread/detail/force_cast.hpp:// force_cast will convert anything to anything.  
boost/thread/detail/force_cast.hpp:inline Return_Type &force_cast(Argument_Type &rSrc)  
boost/thread/detail/force_cast.hpp:inline const Return_Type &force_cast(const Argument_Type &rSrc)  
new-host:include viboes$ grep -r singleton *  
boost/thread/detail/singleton.hpp:// class singleton has the same goal as all singletons: create one instance of  
boost/thread/detail/singleton.hpp:class singleton : private T  
boost/thread/detail/singleton.hpp:    singleton();  
boost/thread/detail/singleton.hpp:    ~singleton();  
boost/thread/detail/singleton.hpp:inline singleton<T>::singleton()  
boost/thread/detail/singleton.hpp:inline singleton<T>::~singleton()  
boost/thread/detail/singleton.hpp:/*static*/ T &singleton<T>::instance()  
boost/thread/detail/singleton.hpp:    static singleton<T> s_oT;  
new-host:include viboes$ cd ../src/  
new-host:src viboes$ grep -r force_cast *  
new-host:src viboes$ grep -r singleton *  
```  
  
Those files are not part of the interface.  
  
I could apply your patch, but I would like to have a better reason.

---

## Comment 5

> Username: viboes  
> Created_at: 2016-05-25 21:14:29 UTC  
> Url: https://github.com/boostorg/thread/pull/83#issuecomment-221709657  

Which code will be break the change to change the namespaces in these files to thread_detail or even to remove the files?

---

## Comment 6

> Username: v4hn  
> Created_at: 2016-05-25 23:18:14 UTC  
> Url: https://github.com/boostorg/thread/pull/83#issuecomment-221736001  

I suppose they are not required by the project anymore.  
They were added in 2002 (wow..) in commit 6bc82a8580072812c34b3fb373233743d6d9737c .  
Looks like someone didn't clean them up?  
  
I found the headers, because someone at shadow robotics decided to make use of them, even though they are in the details namespace: https://github.com/shadow-robot/sr_core/blob/indigo-devel/sr_utilities/include/sr_utilities/sr_math_utils.hpp#L33  
  
From your perspective, it's likely a good idea to get rid of the two files and make lazy (ab)users of them use different implementations/implement them on their own.  
I still think that the additional specifiers in this request make sense to add as this makes the usage of the boost::thread class consistent in this header. I don't have a strong opinion there though

---

## Comment 7

> Username: viboes  
> Created_at: 2016-05-26 05:39:21 UTC  
> Url: https://github.com/boostorg/thread/pull/83#issuecomment-221782324  

Yes, I believe that it is better that they use their own implementation. None of them is complex and has no dependencies.  
  
I would merge your changes as it doesn't hurt and it could avoid future issues.

---
