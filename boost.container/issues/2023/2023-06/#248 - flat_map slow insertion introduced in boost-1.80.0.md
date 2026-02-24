# #248 - flat_map slow insertion introduced in boost-1.80.0 [Closed]

> Username: biljazovic  
> Created at: 2023-06-01 14:15:49 UTC  
> Updated at: 2025-09-07 18:44:12 UTC  
> Closed at: 2025-09-07 18:44:12 UTC  
> Url: https://github.com/boostorg/container/issues/248  

Hello,  
  
I noticed that inserting elements in ```flat_map<int,int>``` is around 2 times slower going from version boost-1.79.0 to boost-1.80.0. I've found the relevant commit, [0d5068](https://github.com/boostorg/container/commit/0d5068a0cc8b9aa74776bfc256759b0e8096eb71).  
  
Sample code that is 2x faster when said commit is reverted:  
```c++  
#include <boost/container/flat_map.hpp>  
  
int main() {  
	boost::container::flat_map<int, int> m_flat_map;  
	for (int i = 100000; i >= 0; --i) m_flat_map[i] = 1;  
	return 0;  
}  
```  
compiler: clang 15.0.7, linux-gnu platform

---

## Comment 1

> Username: psiha  
> Created at: 2023-12-21 12:38:58 UTC  
> Url: https://github.com/boostorg/container/issues/248#issuecomment-1866177980  

upvote

---

## Comment 2

> Username: nigels-com  
> Created at: 2025-09-05 06:19:02 UTC  
> Url: https://github.com/boostorg/container/issues/248#issuecomment-3257202777  

Possibly related to Issue #300 and PR #314.  
  
I'll go ahead and file a PR to revert the `pair.hpp` changes.  
It probably isn't worth a performance regression for a compiler warning that can likely be resolved in a different manner.

---

## Comment 3

> Username: nigels-com  
> Created at: 2025-09-06 22:15:38 UTC  
> Url: https://github.com/boostorg/container/issues/248#issuecomment-3263252160  

Should be resolved now, and in the next boost release. Via PR #317

---

## Comment 4

> Username: igaztanaga  
> Created at: 2025-09-07 18:44:12 UTC  
> Url: https://github.com/boostorg/container/issues/248#issuecomment-3263966152  

Since recent Boost.Container versions use std::pair internally instead of the old dtl::pair, I've also applied optimization traits to std::pair (https://github.com/boostorg/container/commit/9552828c54a6729510d9d8935616055996586644).
