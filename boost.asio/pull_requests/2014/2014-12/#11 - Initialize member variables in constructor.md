# #11 Initialize member variables in constructor [Closed]

> Username: tempoz  
> Created at: 2014-12-08 21:13:40 UTC  
> Updated at: 2017-12-02 08:08:43 UTC  
> Closed at: 2017-12-02 08:08:43 UTC  
> Url: https://github.com/boostorg/asio/pull/11  

Default value-initialize these member variables of the object, as they are not themselves and objects and thus can not have default constructors called automatically to initialize them.  
This defect was uncovered by Coverity and has Coverity issue id CID14974.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2017-12-02 08:08:43 UTC  
> Url: https://github.com/boostorg/asio/pull/11#issuecomment-348676394  

Won't fix. The variables are initialised with their intended values (i.e. they are not just default constructed) after the object is retrieved from the free list.

---
