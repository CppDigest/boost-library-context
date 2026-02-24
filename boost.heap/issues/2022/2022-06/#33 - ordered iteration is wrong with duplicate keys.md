# #33 - ordered iteration is wrong with duplicate keys [Open]

> Username: llxxee  
> Created at: 2022-06-03 23:25:17 UTC  
> Updated at: 2022-06-03 23:25:17 UTC  
> Url: https://github.com/boostorg/heap/issues/33  

To repro:  
https://godbolt.org/z/jbK7nz37E  
```  
#include <iostream>  
#include <boost/heap/binomial_heap.hpp>  
  
int main()  
{  
  boost::heap::binomial_heap<int> heap;  
  heap.push(100);  
  heap.push(100);  
  
  
  int count = 0;  
  for (auto it = heap.ordered_begin();  
        it != heap.ordered_end();  
        ++it) {  
    std::cout << *it << std::endl;  
    count++;  
  }  
  
  // expected 2, got 3  
  std::cout << count << std::endl;  
}  
```
