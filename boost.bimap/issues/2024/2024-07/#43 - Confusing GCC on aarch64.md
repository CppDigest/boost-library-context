# #43 - Confusing GCC on aarch64 [Closed]

> Username: iazz  
> Created at: 2024-07-03 12:51:16 UTC  
> Updated at: 2024-09-03 15:38:38 UTC  
> Closed at: 2024-09-03 15:38:38 UTC  
> Url: https://github.com/boostorg/bimap/issues/43  

The following code compiles just fine, but on aarch64, with -O2, with a recent GCC (at least from version 11 and until the very tip of the master branch), the last assert fails:  
  
```  
#include <cassert>  
#include <boost/bimap.hpp>  
#include <boost/bimap/unordered_set_of.hpp>  
  
int main()  
{  
  using map_type = boost::bimap<boost::bimaps::set_of<int>,  
				boost::bimaps::unordered_set_of<int>>;  
  map_type	map;  
  auto	x = map.insert({0, 0}).first;  
  map.replace_right(x, 42);  
  auto	y = map.find(map_type::value_type{0, 42});  
  assert(y != map.end());  
  assert(x == y);  
  assert(&x->right == &y->right);  
  assert(y->right != 0);  
  assert(x->right != 0);  
}  
```  
With -O2 -fno-strict-aliasing, or with -O1 or -O0, all the asserts pass.  
  
I've filed a bug in GCC (https://gcc.gnu.org/bugzilla/show_bug.cgi?id=115767), but I'm afraid this can be a bug in Boost Bimap instead.  At least it would be very beneficial to further reduce this code to its absolute minimum, so I'm asking for help the library authors.  
  
Thanks.

---

## Comment 1

> Username: iazz  
> Created at: 2024-07-24 13:31:28 UTC  
> Url: https://github.com/boostorg/bimap/issues/43#issuecomment-2247945329  

After much fiddling, it looks like this problem appears on other architectures too, provided they offer enough registers, I guess.  
  
Pull request with a fix is coming up.
