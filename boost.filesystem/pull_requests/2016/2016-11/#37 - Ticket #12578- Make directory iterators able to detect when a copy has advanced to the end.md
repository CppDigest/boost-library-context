# #37 Ticket #12578: Make directory iterators able to detect when a copy has advanced to the end [Closed]

> Username: mlimber  
> Created at: 2016-11-22 19:50:02 UTC  
> Updated at: 2016-11-23 13:15:14 UTC  
> Closed at: 2016-11-23 13:04:55 UTC  
> Url: https://github.com/boostorg/filesystem/pull/37  

Motivating example is using an iterator (e.g., testing to see if it is at the end) after a copy of it has been advanced to the end:  
```cpp  
auto di = fs::directory_iterator( "." );  
const auto end = fs::directory_iterator();  
const auto dist = std::distance( di, end ); // implicitly iterate a copy of di to the end  
                                            // which makes di undetectably invalid.  
std::cout << "File count: " << dist << std::endl;  
while( di != end ) // crash! Also with: for( const auto& e : di ), etc.  
{  
  std::cout << *di++ << std::endl;  
}  
```  
For details, see [Ticket #12578](https://svn.boost.org/trac/boost/ticket/12578).

---

## Comment 1

> Username: Beman  
> Created_at: 2016-11-23 13:04:55 UTC  
> Url: https://github.com/boostorg/filesystem/pull/37#issuecomment-262507488  

I've applied essentially the same fix as your pull request, but with some added comments and a different set of test cases. By the way, the motivating example you give does a post-increment on an iterator than will be the end iterator the last time through the loop.  
  
Thanks for the bug report!  
  
--Beman

---

## Comment 2

> Username: mlimber  
> Created_at: 2016-11-23 13:15:14 UTC  
> Url: https://github.com/boostorg/filesystem/pull/37#issuecomment-262509643  

Fixed the increment. Thanks for processing it so quickly!

---
