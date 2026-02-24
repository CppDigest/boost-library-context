# #16 Fix missing include <iterator> [Merged]

> Username: aldonin  
> Created at: 2016-04-27 15:12:41 UTC  
> Updated at: 2016-04-27 17:56:56 UTC  
> Merged at: 2016-04-27 17:56:56 UTC  
> Closed at: 2016-04-27 17:56:56 UTC  
> Url: https://github.com/boostorg/algorithm/pull/16  

I got compilation error, because of using of undeclared identifier 'front_inserter'.  
I checked that [std::front_inseter](http://www.cplusplus.com/reference/iterator/front_inserter) is defined at [<iterator>](http://www.cplusplus.com/reference/iterator) header, which probably is missed here.   
  
So this fix is just about adding one more include of  [<iterator>](http://www.cplusplus.com/reference/iterator) to the sample.  
  
Checked with VS 2015 community.

---
