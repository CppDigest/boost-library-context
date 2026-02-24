# #115 Use real_allocator to find allocator when rebinding [Closed]

> Username: ecatmur  
> Created at: 2019-04-17 13:55:11 UTC  
> Updated at: 2019-04-24 20:46:28 UTC  
> Closed at: 2019-04-24 20:46:28 UTC  
> Url: https://github.com/boostorg/container/pull/115  

After 83bb62fed3aca489e447e394f3b23c272712ecb7 the allocator parameter of  
e.g. small_vector can be void. Use real_allocator to convert this to the  
actual allocator used by the container.  
  
example (works up to 1.69, breaks in 1.70):  
  
    #include <boost/container/flat_map.hpp>  
    #include <boost/container/small_vector.hpp>  
    boost::container::flat_map<int, int, std::less<>,  
        boost::container::small_vector<std::pair<int, int>, 7>> m;

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-04-24 20:46:28 UTC  
> Url: https://github.com/boostorg/container/pull/115#issuecomment-486419074  

Thanks for the report. I've cherry-picked the first commit:  
  
https://github.com/boostorg/container/commit/b075bce22ebbdbbe2a846820ae8ba2d410099a14  
  
Thanks to your test case, I've found another problem: Issue #118. I will add new test cases to test flat_map and flat_set as adaptors.

---
