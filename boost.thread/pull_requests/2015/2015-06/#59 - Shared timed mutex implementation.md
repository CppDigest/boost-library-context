# #59 Shared timed mutex implementation [Closed]

> Username: eldiener  
> Created at: 2015-06-28 23:15:01 UTC  
> Updated at: 2015-09-27 22:17:39 UTC  
> Closed at: 2015-09-27 22:17:39 UTC  
> Url: https://github.com/boostorg/thread/pull/59  

Updated to have shared_timed_mutex class in place of shared_mutex class as per the latest C14 standard. The shared_mutex class is a typedef to shared_timed_mutex, but should be deprecated in the thread docs so that eventually shared_mutex can be a separate non-timed shared_mutex when the C17 standard occurs.  
  
I did not change the documentation so that the deprecation of shared_mutex can be documented and end-users encouraged to use shared_timed_mutex instead. I leave this for the thread developer(s) to do.  
  
I also did not check for other Boost libraries using shared_mutex. But since shared_mutex is now always a typedef for shared_timed_mutex, all uses of shared_mutex should continue to work as before.

---

## Comment 1

> Username: viboes  
> Created_at: 2015-06-29 06:07:34 UTC  
> Url: https://github.com/boostorg/thread/pull/59#issuecomment-116461587  

What do you think of duplicating the classes timed and not timed and deprecate the timed operations on the not timed classes?

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-06-29 12:41:56 UTC  
> Url: https://github.com/boostorg/thread/pull/59#issuecomment-116643081  

"What do you think of duplicating the classes timed and not timed and deprecate the timed operations on the not timed classes? "  
  
You could do that, but does not the typedef 'duplicate' the classes adequately right now ? I just coded the easiest solution at the present time.  
  
In the future, as C++17 is implemented by compilers and you have already given warning that shared_mutex has been deprecated in favor of share_timed_mutex, you can remove the typedef and actually create a separate non-timed shared_mutex class.  
  
I think it is important now that you add to the documentation that shared_mutex is being deprecated in favor of shared_timed_mutex since shared_mutex's interface will change in the future to a non-timed class. Then if you actually code a non-timed shared_mutex sometime in the future end-users will have been given adequate warning that continued use of the timed operations of shared_mutex will break their code.

---

## Comment 3

> Username: viboes  
> Created_at: 2015-06-30 21:55:27 UTC  
> Url: https://github.com/boostorg/thread/pull/59#issuecomment-117355938  

But   
  
```  
typedef shared_mutex shared_timed_mutex;   
```  
  
will do what you want also, isn't it?

---

## Comment 4

> Username: eldiener  
> Created_at: 2015-06-30 22:04:15 UTC  
> Url: https://github.com/boostorg/thread/pull/59#issuecomment-117358026  

Yes.   
  
I preferred the other way because in the future you are going to want to keep shared_timed_mutex and change shared_mutex.

---
