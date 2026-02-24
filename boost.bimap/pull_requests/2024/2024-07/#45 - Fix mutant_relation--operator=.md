# #45 Fix mutant_relation::operator=. [Merged]

> Username: iazz  
> Created at: 2024-07-24 13:37:49 UTC  
> Updated at: 2024-09-03 15:38:38 UTC  
> Merged at: 2024-09-03 15:38:37 UTC  
> Closed at: 2024-09-03 15:38:37 UTC  
> Url: https://github.com/boostorg/bimap/pull/45  

Defining  
```  
  template <bool FM>  
  mutant_relation& operator=(const mutant_relation<TA, TB, Info, FM>& rel) {  
    base_::change_to(rel);  
    return *this;  
  }  
```  
does not prevent the compiler from implicitly providing  
```  
  mutant_relation& operator=(const mutant_relation&);  
```  
and hence the implicit version takes over when `FM == force_mutable` and does not call `base_::change_to`.  
  
Replace the template version with two non-template overloads, both calling `base_::change_to`.  
  
~~Apparently fixes #43.~~  
  
This makes issue #43 only go away on x86_64, but it is still present on aarch64 when compiling with at least -O2.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-08-06 23:15:28 UTC  
> Url: https://github.com/boostorg/bimap/pull/45#issuecomment-2272316511  

Better to not use `not` here. Use the old-school `!` instead.

---

## Comment 2

> Username: joaquintides  
> Created_at: 2024-08-09 11:39:34 UTC  
> Updated_at: 2024-08-09 15:22:15 UTC  
> Url: https://github.com/boostorg/bimap/pull/45#issuecomment-2277755363  

Hi Ignacy and Peter, my analysis of the problem and the (partial) fix follows. Original test case pasted here for convenience:  
  
```cpp  
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
* This is certainly a compiler bug. `x` and `y` are of the same type and have the same internal value (verified with a debugger), so if `assert(y->right != 0)` does pass so must `assert(x->right != 0)`.  
* `mutant_relation& operator=` is called in the following [context](https://github.com/boostorg/multi_index/blob/5c17744f34130451a97c9139b7fec454a7afbc3c/include/boost/multi_index/detail/index_base.hpp#L178-L182):  
```cpp  
  bool replace_(const value_type& v,index_node_type* x,lvalue_tag)  
  {  
    x->value()=v;  
    return true;  
  }  
```  
`mutant_relation& operator=(const mutant_relation<TA,TB,Info,!force_mutable>&)` is (obviously) not called but the default-generated assignment operator is, which does exactly the same as `base_::change_to(rel)` (change the values of the pair being modified).  
* That said, adding a explicit `mutant_relation& operator=(const mutant_relation&)` overload does not hurt, so I think we can go with the proposed PR if that helps (partally, from what I understand).  
  
Nitpick: your code is tab-indented, please replace with spaces, thank you!

---

## Comment 3

> Username: iazz  
> Created_at: 2024-08-09 20:28:11 UTC  
> Url: https://github.com/boostorg/bimap/pull/45#issuecomment-2278701148  

>     * This is certainly a compiler bug. `x` and `y` are of the same type and have the same internal value (verified with a debugger), so if `assert(y->right != 0)` does pass so must `assert(y->right != 0)`.  
  
The fact that this behaves strangely doesn't necessarily imply a compiler bug, especially if the code violates strict aliasing rules.  As I see it, there are various places in Bimap and Multi-Index code where strict aliasing violation warnings are purposefully disabled and a few reinterpret_cast-based hacks are performed.  
  
>     * `mutant_relation& operator=` is called in the following [context](https://github.com/boostorg/multi_index/blob/5c17744f34130451a97c9139b7fec454a7afbc3c/include/boost/multi_index/detail/index_base.hpp#L178-L182):  
>   
>   
> ```c++  
>   bool replace_(const value_type& v,index_node_type* x,lvalue_tag)  
>   {  
>     x->value()=v;  
>     return true;  
>   }  
> ```  
>   
> `mutant_relation& operator=(const mutant_relation<TA,TB,Info,!force_mutable>&)` is (obviously) not called but the default-generated assignment operator is, which does exactly the same as `base_::change_to(rel)` (change the values of the pair being modified).  
>   
>     * That said, adding a explicit `mutant_relation& operator=(const mutant_relation&)` overload does not hurt, so I think we can go with the proposed PR if that helps (partally, from what I understand).  
  
You're right, the code is supposed to be equivalent, but first, GCC with -Wdeprecated-copy (which is enabled with -W) warns on the lack of explicitly defined assignment operator as being deprecated (because a copy constructor has been explicitly defined), and second, apparently GCC generates different code depending on there being an explicit assignment operator defined or not.  
  
> Nitpick: your code is tab-indented, please replace with spaces, thank you!  
  
Okay, I'm fixing that right away.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-08-17 18:41:35 UTC  
> Url: https://github.com/boostorg/bimap/pull/45#issuecomment-2294939381  

Closing/reopening to retrigger CI.

---
