# #98 - Intrusive list iterator declaration for incomplete type does not compile (std::list and boost::container::list do)... [Closed]

> Username: nbooster  
> Created at: 2025-10-31 10:47:43 UTC  
> Updated at: 2025-11-03 22:53:00 UTC  
> Closed at: 2025-11-03 07:52:52 UTC  
> Url: https://github.com/boostorg/intrusive/issues/98  

Hello,  
  
I just want to ask if there is a way to make this code compile:  
  
```cpp  
// example.hpp  
#include <list>  
#include <boost/container/list.hpp>  
#include <boost/intrusive/list.hpp>  
  
class LL;  
  
class O  
{  
    std::list<LL>::iterator stlIt;  
    boost::container::list<LL>::iterator boostIt;  
    // uncommenting this line the code does not compile...  
    // boost::intrusive::list<LL>::iterator boostIntrusiveIt;  
};  
  
class OL  
{  
    O member;  
};  
  
class L  
{  
    OL member;  
};  
  
class LL  
{  
    L member;  
    boost::intrusive::list_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link>> listHook_;  
};  
```  
  
g++ 15.2.0 (Boost 1.88)...  
  
The normal list iterators seem to be OK,  
but the intrusive one is not...  
  
*What about SCARY iterators ( https://www.boost.org/doc/libs/1_70_0/doc/html/intrusive/boost_intrusive_iterators.html ) ?  
**How fast is this: s_iterator_to ??

---

## Comment 1

> Username: RFoe  
> Created at: 2025-11-03 07:36:44 UTC  
> Url: https://github.com/boostorg/intrusive/issues/98#issuecomment-3479258129  

i met the similar problem as well. slist/list seemingly contains a dummy sentinel of the value type(at least in boost 1.88.0 which i'm using), which makes incomplete value type not allowed.  
  
my issue post is here:  
https://github.com/boostorg/intrusive/issues/100

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-11-03 07:52:52 UTC  
> Url: https://github.com/boostorg/intrusive/issues/98#issuecomment-3479300187  

It's not about iterators, but about containers.  
  
Intrusive containers don't support incomplete types, not sure how hard that can be without breaking code. Using containers instead of iterators yields to the same problem:  
  
```  
class O  
{  
    std::list<LL>::iterator stlIt;  
    boost::container::list<LL> boostcont;  
    // uncommenting this line the code does not compile...  
    // boost::intrusive::list<LL> boostIntrusivecont;  
};  
```

---

## Comment 3

> Username: nbooster  
> Created at: 2025-11-03 12:07:51 UTC  
> Url: https://github.com/boostorg/intrusive/issues/98#issuecomment-3480202816  

What you are saying is true, but the logic is not very clear.  
  
std::list does not support incomplete types either (which is understandable to an extent),   
but it does support incomplete types iterators...  
  
boost::container::list supports both...  
  
My view is that boost::intrusive::list should support at least iterators of incomplete types... (just like std::list)  
(the type of the iterator itself should not be dependent on the item type of the container).  
  
Example:  
  
```cpp  
#include <list>  
#include <boost/container/list.hpp>  
#include <boost/intrusive/list.hpp>  
  
class A;  
  
class B  
{  
    //std::list<A> list; // <-- this does not compile (as it shouldn't)  
    std::list<A>::iterator iterator; // <-- this compiles (as it should)  
};  
  
class C  
{  
    boost::container::list<A> list; // <-- this shouldn't compile (but it does !!!)  
    boost::container::list<A>::iterator iterator; // <-- this should compile (and it does)  
};  
  
class D  
{  
    //boost::intrusive::list<A> list; // <-- this does not compile (as it shouldn't)  
    //boost::intrusive::list<A>::iterator iterator; // <-- this should compile (BUT IT DOES NOT !!!)  
};  
  
class A  
{  
    int item;  
};  
  
int main()  
{  
    B b;  
  
    C c;  
  
    D d;  
  
    return 0;  
}  
```  
  
https://godbolt.org/z/xce71YKfc  
  
Maybe it's something for the feature to consider...  
  
Anyway, thanks for the answer.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2025-11-03 21:50:15 UTC  
> Url: https://github.com/boostorg/intrusive/issues/98#issuecomment-3482735746  

Yes, it's an interesting feature to add for Boost.Intrusive. The type of the iterator must depend on the container's `value_type` because `operator *()` must return a reference to that type. Accessing the internal `::iterator` type of the container triggers the instantiation of the container (which might require a complete `value_type`.  
  
The additional tricky part is to avoid any instantiation of the `value_type` when instantiating the iterator, except when `operator*` is instantiated. It might require quite a bit of refactoring inside the container.  
  
It's not straightforward, I'm afraid, but maybe worth trying...

---

## Comment 5

> Username: nbooster  
> Created at: 2025-11-03 22:48:39 UTC  
> Updated at: 2025-11-03 22:53:00 UTC  
> Url: https://github.com/boostorg/intrusive/issues/98#issuecomment-3482921944  

You used the correct word... "instantiation".  
  
Since the container and the value_type never get explicitly instantiated (only an iterator to value_type) and operator*() never gets called... there should not be any problem.  
  
If we want to be 100% correct, in theory, instantiating a container's iterator should not instantiate the container itself in the first place (just like the instantiation of a pointer to an object does not instantiate, nor requires the instantiation, of the object itself).  
  
Remember, an iterator is a separate object type (the fact that we nest it usually inside the container class is just to make it's usage easier, but refactoring this would require huge work and would also cause headaches in existed code bases).  
  
Iterators should only hold references or pointers to value_type objects (or to objects that contain value_type objects),  
and that is totally fine by the standard ( https://stackoverflow.com/questions/7356603/references-to-incomplete-types ).   
  
The focus, at first glance I think, should be to completely avoid the instantiation of value_type when the container gets instantiated, just like boost::container::list does and std::list does not...
