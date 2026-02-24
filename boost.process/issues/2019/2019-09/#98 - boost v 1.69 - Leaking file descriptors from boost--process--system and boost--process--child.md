# #98 - boost v 1.69 - Leaking file descriptors from boost::process::system and boost::process::child [Closed]

> Username: jussihi  
> Created at: 2019-09-02 09:22:49 UTC  
> Updated at: 2019-09-02 15:09:35 UTC  
> Closed at: 2019-09-02 15:09:35 UTC  
> Url: https://github.com/boostorg/process/issues/98  

I already opened a stackoverflow question, but it seems like this is a bug.  
  
Let's say we have this simple code to flush iptables config every 3 seconds (just an example):  
  
```  
#include <boost/process.hpp>  
#include <thread>  
  
  
int main(void)  
{  
  while(true)  
  {  
    std::this_thread::sleep_for(std::chrono::seconds(3));  
    boost::process::system(boost::process::search_path("iptables"), "-F");  
  }  
  return 0;  
}  
```  
  
If we observe the count of open file descriptors by listing `/proc/PID/fd |wc -l`, we can see that the count increases by one every 3 seconds. Eventually, when it reaches 1024, the program will abort, because the system call will throw an exception with what() stating that there are `too many open files!`  
  
Sorry if this is an already known issue.

---

## Comment 1

> Username: a-andre  
> Created at: 2019-09-02 14:36:01 UTC  
> Url: https://github.com/boostorg/process/issues/98#issuecomment-527169753  

Seems to be a duplicate of #62

---

## Comment 2

> Username: jussihi  
> Created at: 2019-09-02 15:09:35 UTC  
> Url: https://github.com/boostorg/process/issues/98#issuecomment-527179903  

@a-andre , thanks, I will close this one.
