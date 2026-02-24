# #21 Fix missing #include causing compilation errors [Merged]

> Username: lorenzb  
> Created at: 2014-10-15 13:10:22 UTC  
> Updated at: 2014-11-02 20:38:20 UTC  
> Merged at: 2014-11-02 20:38:20 UTC  
> Closed at: 2014-11-02 20:38:20 UTC  
> Url: https://github.com/boostorg/graph/pull/21  

I had trouble compiling this simple program and traced it down to a missing include.  
The error occurs in both, boost 1.56.0 and the current develop branch.  
  
```  
#include <boost/graph/adjacency_matrix.hpp>  
  
int main(void) {  
}  
```

---
