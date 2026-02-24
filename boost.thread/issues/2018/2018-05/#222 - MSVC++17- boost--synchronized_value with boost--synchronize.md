# #222 - MSVC++17: boost::synchronized_value with boost::synchronize [Closed]

> Username: SeeSoftware  
> Created at: 2018-05-24 13:30:38 UTC  
> Updated at: 2018-10-07 17:07:49 UTC  
> Closed at: 2018-10-07 17:07:49 UTC  
> Url: https://github.com/boostorg/thread/issues/222  

I'm trying to compile simple code using boost:synchronized_value with boost::synchronize but i'm having problems doing that:  
  
    #include <iostream>  
    #include <boost\thread\synchronized_value.hpp>  
  
    int main()  
    {  
	    boost::synchronized_value<int> val1 = 10;  
	    boost::synchronized_value<int> val2 = 20;  
  
	    auto tuple = boost::synchronize(val1, val2);  
  
	    std::cout << *std::get<0>(tuple) << *std::get<1>(tuple) << "\n";  
  
	    return 0;  
    }  
  
will result in this error:  
  
    boost::mutex::mutex(const boost::mutex &)" : Es wurde versucht, auf eine gelöschte Funktion zu verweisen  
    (use of deleted function)  
  
What i have found out is that this only happens with MSVC++ compilers and only with /std:c++17  
if i try to use c++14 it will compile fine, but i want to use c++17 features.  
I installed boost with vcpkg if that matters.

---

## Comment 1

> Username: viboes  
> Created at: 2018-05-25 07:35:07 UTC  
> Url: https://github.com/boostorg/thread/issues/222#issuecomment-391968405  

This works only if the compiler implements copy-elision. I don't know about what MSVC has changed between those two versions. The single thing we can do is to inhibit this interface for compilers that don't implement copy elision.  
  
PR are welcome.

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-10-04 22:45:16 UTC  
> Updated at: 2018-10-04 22:46:40 UTC  
> Url: https://github.com/boostorg/thread/issues/222#issuecomment-427193380  

@SeeSoftware I have opened a PR #240 that solves the problem.

---

## Comment 3

> Username: viboes  
> Created at: 2018-10-05 04:56:51 UTC  
> Url: https://github.com/boostorg/thread/issues/222#issuecomment-427246339  

@Kojoley thanks for working on this.   
Please, could you explain how this solves the issue?

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-10-05 11:17:14 UTC  
> Url: https://github.com/boostorg/thread/issues/222#issuecomment-427330681  

Because `boost::is_mutex_type<boost::mutex>::value` is false the wrong overload is chosen,  https://github.com/boostorg/thread/blob/fc08c1fe2840baeeee143440fba31ef9e9a813c8/include/boost/thread/lock_algorithms.hpp#L166 instead of https://github.com/boostorg/thread/blob/fc08c1fe2840baeeee143440fba31ef9e9a813c8/include/boost/thread/lock_algorithms.hpp#L144. And because that overload takes arguments by value it tries to find a copy constructor on mutex which is deleted.
