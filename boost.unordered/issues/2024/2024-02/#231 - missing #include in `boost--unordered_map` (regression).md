# #231 - missing #include in `boost::unordered_map` (regression) [Closed]

> Username: joaquintides  
> Created at: 2024-02-08 17:14:45 UTC  
> Updated at: 2024-02-09 16:28:11 UTC  
> Closed at: 2024-02-09 16:28:11 UTC  
> Url: https://github.com/boostorg/unordered/issues/231  

The following fails to compile in VS 2022 with Boost 1.84 and default configuration parameters:  
```cpp  
#include <boost/unordered_map.hpp>  
  
int main()  
{  
  boost::unordered_map<int,int> m;  
  m.emplace(0,0); // error C2039: 'forward_as_tuple': is not a member of 'std'  
}  
```  
This is a regression with respect to Boost 1.83.

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-02-09 16:28:11 UTC  
> Url: https://github.com/boostorg/unordered/issues/231#issuecomment-1936227042  

Fixed in:  
* 43186b04d1b6da9b7a4b16d833ab30c330484eac  
* fa895edac2225c03fdf6dfff3dbe542ce9b8da26  
* c4dbda0f78adfbe0483e193b2edc99111dcfc73b  
* 1df37051ae8c433d42342eb7fbd071fa2b847652
