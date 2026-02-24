# #110 - lockfree::queue too many headers dependencies [Closed]

> Username: damondd  
> Created at: 2025-02-27 16:17:07 UTC  
> Updated at: 2025-02-28 01:01:15 UTC  
> Closed at: 2025-02-28 01:01:14 UTC  
> Url: https://github.com/boostorg/lockfree/issues/110  

Trying to reduce boost headers dependencies for an old project using boost/lockfree/queue.hpp (runtime create boost::lockfree::queue(size)).  
However, it's really frustrating...  
  
Once it includes :  
boost/config.hpp  
boost/config/user.hpp  
boost/parameter  
Then I have to put almost everything in boost 1.87 to make it compiling....., that is:  
  
$ find boost |wc -l  
2444  
files in total  
For just using a queue, i don't know what to say...

---

## Comment 1

> Username: timblechmann  
> Created at: 2025-02-28 01:01:14 UTC  
> Url: https://github.com/boostorg/lockfree/issues/110#issuecomment-2689462850  

> For just using a queue, i don't know what to say...  
  
feel free to use an alternative
