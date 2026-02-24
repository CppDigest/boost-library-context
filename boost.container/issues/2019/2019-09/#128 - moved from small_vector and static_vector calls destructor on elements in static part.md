# #128 - moved from small_vector and static_vector calls destructor on elements in static part [Closed]

> Username: slymz  
> Created at: 2019-09-09 21:51:45 UTC  
> Updated at: 2020-07-12 11:52:00 UTC  
> Closed at: 2020-07-12 11:48:47 UTC  
> Url: https://github.com/boostorg/container/issues/128  

When a `small_vector` or `static_vector` is moved from they continue ownership of elements:  
  
```  
using Container = boost::small_vector<TypeWithNonTrivialDestructor, 3>;  
Container c1;  
c1.resize(3);  
Container c2(std::move(c1));  
// on scope exit, 6 calls are made, 2 per each element  
```  
See full code and reproduction here: https://godbolt.org/z/MHA31e  
  
This is possibly not a bug, as the underlying elements are moved from correctly.   
  
However, it is a very counter intuitive behavior that makes it very tricky to use as a drop in replacement for `std::vector`.   
  
Documentation of `boost::container::vector::vector(vector&& x)` simply says:  
  
> Effects: Move constructor. Moves x's resources to *this.  
  
This is somewhat misleading. Documentation could be improved (specialized?) for these special containers by emphasizing this subtle behavior.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-07-12 11:52:00 UTC  
> Url: https://github.com/boostorg/container/issues/128#issuecomment-657211429  

Many thanks for the report!  
  
This is not a bug, as elements are properly destroyed, but as you mention, is a counter intuitive behavior that several users have reported. The problem is not the number of destructor calls (destructor calls will be always more for static/small vector, as the underlying buffer can't be moved and new objects must be constructed in target object's buffer, the problem is that the source object is not immediately cleared.  
  
Even more counter-intuitive in small vector, as depending if the internal buffer is used or not, the source object might or might not be cleared. Changed so that the source object is always emptied, obtaining the same result as std::vector.
