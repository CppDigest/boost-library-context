# #152 - Tree-based containers have troubles with move-only types [Closed]

> Username: ImplOfAnImpl  
> Created at: 2020-07-20 10:19:17 UTC  
> Updated at: 2020-10-31 22:11:06 UTC  
> Closed at: 2020-10-31 22:10:01 UTC  
> Url: https://github.com/boostorg/container/issues/152  

This issue has been reported a couple of years ago [here](https://svn.boost.org/trac10/ticket/13405) and a fix had been proposed.  
In short, the following won't compile:  
```  
#include <boost/container/set.hpp>  
#include <memory>  
  
int main()  
{  
    boost::container::set<std::unique_ptr<int>> set1, set2;  
    set2 = std::move(set1);  
}  
```  
I'm not sure why it's being ignored, probably it's because the svn tracker is dead?  
Anyway, I'm re-reporting it here, just in case.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-10-31 22:11:05 UTC  
> Url: https://github.com/boostorg/container/issues/152#issuecomment-719994646  

You are right, the svn tracker is dead, and the bug is certainly important. Many thanks for the report!
