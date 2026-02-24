# #192 - thread_group vector-like initialization [Closed]

> Username: ldionne  
> Created at: 2017-11-15 23:54:45 UTC  
> Updated at: 2018-04-13 06:58:05 UTC  
> Closed at: 2017-11-24 22:28:28 UTC  
> Url: https://github.com/boostorg/thread/issues/192  

`boost::thread_group` could provide a constructor taking a number and a function object:  
  
```c++  
boost::thread_group threads(10, [] { run something });  
```  
  
This is especially useful when mixing threads and something like Boost.ASIO with an `io_service`, or when using Fibers. In that case, the function being run in all the threads is the same, so it makes sense to use a constructor like this.  
  
Another possibility would be to provide an iterator-based constructor (iterators to callables):  
  
```c++  
std::vector<std::function<void()>> functions;  
boost::thread_group threads(functions.begin(), functions.end());  
```  
  
I don't have a use-case for the latter, but I do have one for the former. I can submit a PR if there's interest for any of this.

---

## Comment 1

> Username: viboes  
> Created at: 2017-11-16 00:34:21 UTC  
> Url: https://github.com/boostorg/thread/issues/192#issuecomment-344776213  

I don't like thread_group class. IIRC I deprecated it, but maybe I'm wrong.  
What is missing with a vector of threads.  What make threads different that need a specific container?

---

## Comment 2

> Username: ldionne  
> Created at: 2017-11-17 21:43:17 UTC  
> Url: https://github.com/boostorg/thread/issues/192#issuecomment-345373606  

I don't think it's deprecated in the documentation. Thanks for your comment; I moved away from using `thread_group`. I must agree it does not have much more beyond a straight `std::vector<boost::thread>`.  
  
If you're not going to improve the class and if you think it does not buy much over `std::vector`, I would suggest deprecating it in the documentation to make it clear that users are discouraged from using it. Otherwise (and especially since it's marked as experimental), users may think that it's something that will be expanded in the future and even possibly proposed for standardization.

---

## Comment 3

> Username: viboes  
> Created at: 2017-11-21 06:39:45 UTC  
> Url: https://github.com/boostorg/thread/issues/192#issuecomment-345931574  

I don't think the standard will be interested by a thread_group. But if you believe it is worth improving it, I would accept a PR.

---

## Comment 4

> Username: ldionne  
> Created at: 2017-11-24 22:28:14 UTC  
> Url: https://github.com/boostorg/thread/issues/192#issuecomment-346902437  

What I'm proposing is to mark it as deprecated.
