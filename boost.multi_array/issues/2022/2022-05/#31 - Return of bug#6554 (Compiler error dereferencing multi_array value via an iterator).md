# #31 - Return of bug#6554 (Compiler error dereferencing multi_array value via an iterator) [Open]

> Username: krsch  
> Created at: 2022-05-20 12:11:31 UTC  
> Updated at: 2022-05-20 12:25:30 UTC  
> Url: https://github.com/boostorg/multi_array/issues/31  

The bug described in https://svn.boost.org/trac10/ticket/6554 seems to return. I tried the example from this bug in godbolt: https://godbolt.org/z/4vMf8oo4q and it fails to compile. The code I used is:  
```c++  
#include <boost/multi_array.hpp>  
  
struct data  
{  
	int	value;  
};  
typedef boost::multi_array<data, 2> array_type;  
  
int main() {  
    array_type	a(boost::extents[4][5]);  
  
    // ERROR: Cannot compile this line  
    a.begin()->begin()->value = 7;  
  
    // Compiles successfully  
    (*a.begin()->begin()).value = 5;  
    return 0;  
}  
```  
All versions of boost on godbolt (1.64 to 1.79) seem to be affected.

---

## Comment 1

> Username: krsch  
> Created at: 2022-05-20 12:12:45 UTC  
> Url: https://github.com/boostorg/multi_array/issues/31#issuecomment-1132830912  

Or was this bug never fixed?

---

## Comment 2

> Username: krsch  
> Created at: 2022-05-20 12:25:30 UTC  
> Url: https://github.com/boostorg/multi_array/issues/31#issuecomment-1132841973  

I found no tests covering `operator->`. I can try to fix this bug myself, but I'm afraid to break some code when to tests are present.
