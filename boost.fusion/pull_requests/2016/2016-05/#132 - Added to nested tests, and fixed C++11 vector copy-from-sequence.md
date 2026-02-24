# #132 Added to nested tests, and fixed C++11 vector copy-from-sequence [Merged]

> Username: vtnerd  
> Created at: 2016-05-31 18:27:35 UTC  
> Updated at: 2016-05-31 22:47:22 UTC  
> Merged at: 2016-05-31 22:47:22 UTC  
> Closed at: 2016-05-31 22:47:22 UTC  
> Url: https://github.com/boostorg/fusion/pull/132  

This patch fixes the C++11 copy/move from another fusion sequence type. The one problem with this patch is that `fusion::result_of::size` is not a constant _compile time_ operation for all fusion sequence types.  
  
The most simple case that causes compilation error in C++03 and C++11:  
  
``` c++  
#include <boost/fusion/tuple.hpp>  
#include <boost/fusion/container/vector.hpp>  
int main()  
{  
    typedef boost::fusion::vector<int> nested;  
    const boost::fusion::tuple<nested> og(nested(1));  
    boost::fusion::vector<nested> foo(og);  
    return 0;  
}  
```  
  
whereas this only fails in C++11 mode:  
  
``` c++  
#include <boost/fusion/tuple.hpp>  
#include <boost/fusion/container/vector.hpp>  
int main()  
{  
    typedef boost::fusion::vector<int> nested;  
    const boost::fusion::tuple<nested, nested> og(  
        nested(1), nested(1));   
    boost::fusion::vector<nested, nested> foo(og);  
    return 0;  
}  
```  
  
This patch adds tests for these cases, and fixes the C++11 version. Tests that currently fail are not compiled, and therefore all tests should still pass on develop. I ran all of the tests in C++03 and C++11 mode using Clang 3.5 on OSX - I can test on MSVC or Linux Gcc/Clang if necessary. The C++03 fixes and C++11 fixes for list and deque will have to be in future patches.

---

## Comment 1

> Username: vtnerd  
> Created_at: 2016-05-31 20:54:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/132#issuecomment-222817190  

I just did a forced push overwrite to remove an include that is now unnecessary, so sorry for anybody that was looking at the original commit.

---
