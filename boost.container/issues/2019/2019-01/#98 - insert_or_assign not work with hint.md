# #98 - insert_or_assign not work with hint [Closed]

> Username: tower120  
> Created at: 2019-01-10 15:00:37 UTC  
> Updated at: 2019-02-01 23:26:32 UTC  
> Closed at: 2019-02-01 23:26:32 UTC  
> Url: https://github.com/boostorg/container/issues/98  

The following code doesn't compiles :   
  
https://wandbox.org/permlink/GltFoHe4IcVW9oBd  
  
```cpp  
#include <boost/container/flat_map.hpp>  
  
int main() {  
    boost::container::flat_map<int, int> map;      
    map.insert_or_assign(map.begin(), 1,1);  
    return 0;  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-02-01 23:26:32 UTC  
> Url: https://github.com/boostorg/container/issues/98#issuecomment-459902962  

Many thanks for the report!
