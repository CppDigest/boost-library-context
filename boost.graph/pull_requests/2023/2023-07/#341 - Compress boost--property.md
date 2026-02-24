# #341 Compress boost::property [Merged]

> Username: mglisse  
> Created at: 2023-07-07 20:15:47 UTC  
> Updated at: 2023-09-13 01:12:00 UTC  
> Merged at: 2023-09-13 01:11:59 UTC  
> Closed at: 2023-09-13 01:11:59 UTC  
> Url: https://github.com/boostorg/graph/pull/341  

Fix #142.  
It isn't the nicest fix, but it is the smallest one I can think of and someone is free to do better later. Specializing `property<*,*,no_property>` without m_base would require changing the lookup code that expects this member. Grouping the 2 members in a tuple would also require (syntactic) changes in the rest of the file.  
  
I am not adding a testcase like  
```c++  
#include <boost/static_assert.hpp>  
#include <boost/pending/property.hpp>  
  
struct Tag {};  
struct Reference { double d; };  
typedef boost::property<Tag, Reference> Prop;  
  
int main()  
{  
  BOOST_STATIC_ASSERT(sizeof(Reference) == sizeof(Prop));  
}  
```  
because I don't think Boost.Config provides an easy way to detect platforms where BOOST_ATTRIBUTE_NO_UNIQUE_ADDRESS does something.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2023-09-04 03:16:25 UTC  
> Url: https://github.com/boostorg/graph/pull/341#issuecomment-1704551905  

We don't have any C++23 builds in our CI at the moment, so you'll have to wait for that to be added via a separate PR.

---

## Comment 2

> Username: mglisse  
> Created_at: 2023-09-04 06:38:23 UTC  
> Url: https://github.com/boostorg/graph/pull/341#issuecomment-1704695172  

> We don't have any C++23 builds in our CI at the moment, so you'll have to wait for that to be added via a separate PR.  
  
BOOST_ATTRIBUTE_NO_UNIQUE_ADDRESS expands to `[[no_unique_address]]` for both gcc and clang (except possibly on windows?) even with `-std=c++11`, and the attribute is not ignored.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-09-13 01:11:10 UTC  
> Url: https://github.com/boostorg/graph/pull/341#issuecomment-1716788015  

OK, let's give it a go, thanks!

---
