# #282 - compilation fails on unordered_flat_map with certain key , value [Closed]

> Username: kantan2015  
> Created at: 2024-09-13 04:56:43 UTC  
> Updated at: 2024-09-15 10:29:02 UTC  
> Closed at: 2024-09-14 09:04:03 UTC  
> Url: https://github.com/boostorg/unordered/issues/282  

unordered_flat_map::try_emplace fails to compile with certain key, value type.  
Key : boost::flyweight<std::string>  
Value: std::vector with move only type  
  
version: gcc13.2, boost 1.85  
  
```  
#include <unordered_map>  
#include <boost/unordered/unordered_map.hpp>  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <string>  
#include <boost/flyweight.hpp>  
  
template<class Map, class Key>   
void try_emplace(Key key) {  
    Map map;  
    map.try_emplace(key);  
}   
  
  
using move_only_vec = std::vector<std::unique_ptr<int>>;  
 try_emplace<boost::unordered_flat_map<int, move_only_vec>>(1);   // ok  
  
 boost::flyweight<std::string> s("1");   
 try_emplace<std::unordered_map<boost::flyweight<std::string>, move_only_vec>>(s); // ok  
 try_emplace<boost::unordered_map<boost::flyweight<std::string>, move_only_vec>>(s); // ok    
 //try_emplace<boost::unordered_flat_map<boost::flyweight<std::string>, move_only_vec>>(s); // ng     
 try_emplace<boost::unordered_flat_map<boost::flyweight<std::string>, std::unique_ptr<int>>>(s); // ok    
```

---

## Comment 1

> Username: k3DW  
> Created at: 2024-09-13 19:26:27 UTC  
> Updated at: 2024-09-13 19:34:18 UTC  
> Url: https://github.com/boostorg/unordered/issues/282#issuecomment-2350012135  

Looks like it's complaining about trying to copy a `unique_ptr`.  
  
This is happening in `detail::foa::table_core::nosize_transfer_element()`, where it's choosing an unexpected overload. It chooses the "move" overload if at least one of the following conditions are met, otherwise it chooses the "copy" overload.  
* `std::is_nothrow_move_constructible<init_type>::value`  
* `!std::is_same<element_type,value_type>::value`  
* `!std::is_copy_constructible<element_type>::value`  
(Note here, `init_type` is `std::pair<boost::flyweight<std::string>, move_only_vec>`, and `value_type` and `element_type` are both `std::pair<const boost::flyweight<std::string>, move_only_vec>`.)  
  
The 2nd condition can be disregarded here, since it's just a check for `flat` vs `node` containers.  
  
For the 1st condition, `move_only_vec` is nothrow move constructible, but `boost::flyweight<>` isn't.  
For the 3rd condition, `move_only_vec` is (surprisingly) copy constructible in its signature, although it'll give a compile error if you actually try to do it.  
  
To me it looks like the 1st condition is the one that needs "fixing". Without modifying the C++ standard library, this can be fixed by giving Boost.Flyweight types `noexcept` move constructors.  
  
I took a quick cursory look at the Boost.Flyweight library, and I'm not sure how much work this entails, but that's where the issue is coming from. @joaquintides may have more insight.

---

## Comment 2

> Username: joaquintides  
> Created at: 2024-09-14 09:04:03 UTC  
> Updated at: 2024-09-14 09:06:48 UTC  
> Url: https://github.com/boostorg/unordered/issues/282#issuecomment-2350921995  

(Bold mine):  
  
> For the 1st condition, move_only_vec is nothrow move constructible, but boost::flyweight<> isn't.  
For the 3rd condition, **move_only_vec is (surprisingly) copy constructible in its signature**, although it'll give a compile error if you actually try to do it.  
  
This is the core problem: a `std::vector` always promises to be copyable regardless of whether this is actually the case or not --I'm surprised there's no DR filed for this. By contrast, `boost::flyweight` is not promising to be anything it is not.  
  
So, we have two ways of fixing this:  
  
1. Making `boost::flyweight` nothrow move constructible, as you point out. I've filed [a request](https://github.com/boostorg/flyweight/issues/15) for that. This will only solve the OP's problem in Boost 1.87, though.  
2. Wrap `move_only_vec` into a user-defined, non-copyable class:  
  
```cpp  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <boost/flyweight.hpp>  
#include <memory>  
#include <string>  
#include <vector>  
  
template<class Map, class Key>   
void try_emplace(Key key) {  
    Map map;  
    map.try_emplace(key);  
}   
  
int main()  
{  
  struct move_only_vec : std::vector<std::unique_ptr<int>>  
  {  
    move_only_vec() = default;  
    move_only_vec(const move_only_vec&) = delete;  
    move_only_vec(move_only_vec&&) = default;  
  };  
  
  boost::flyweight<std::string> s("1");   
  try_emplace<boost::unordered_flat_map<boost::flyweight<std::string>, move_only_vec>>(s);  
}  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2024-09-14 09:28:01 UTC  
> Url: https://github.com/boostorg/unordered/issues/282#issuecomment-2350928951  

> This is the core problem: a `std::vector` always promises to be copyable regardless of whether this is actually the case or not --I'm surprised there's no DR filed for this.  
  
There is, but it's impossible to fix, because `std::vector` supports incomplete types.

---

## Comment 4

> Username: pdimov  
> Created at: 2024-09-14 10:45:53 UTC  
> Url: https://github.com/boostorg/unordered/issues/282#issuecomment-2350948857  

> 1. Making `boost::flyweight` nothrow move constructible, as you point out. I've filed [a request](https://github.com/boostorg/flyweight/issues/15) for that.  
  
Let's hope the maintainer sees it. :-)

---

## Comment 5

> Username: kantan2015  
> Created at: 2024-09-15 06:19:02 UTC  
> Url: https://github.com/boostorg/unordered/issues/282#issuecomment-2351401572  

Thank you for checking.  
  
Does unordered_flat_map require stronger exception safety than std::unordered_map ?

---

## Comment 6

> Username: joaquintides  
> Created at: 2024-09-15 10:20:59 UTC  
> Url: https://github.com/boostorg/unordered/issues/282#issuecomment-2351517716  

> Does unordered_flat_map require stronger exception safety than std::unordered_map ?  
  
`boost::unordered_flat_map::rehash` and `std::unordered_map::rehash` _provide_ (not require) almost the same [level of exception safety](https://eel.is/c++draft/unord.req#except-4):  
  
_"[I]f an exception is thrown from within a `rehash()` function other than by the container's hash function or comparison function, the `rehash()` function has no effect."_  
  
Now, `std::unordered_map` is a node-based container, meaning its elements are never copied/moved around, so providing this level of exception safety on `rehash` is straightforward. `boost::unordered_flat_map`, on the other hand, stores the elements directly in the bucket array, and rehashing involves transferring those elements from the old to the new bucket array. To meet the exception safety requirements, and be as efficient as possible, rehashing:  
  
* Moves elements if movement is `noexcept`; this meets the exception safety requirements because nothing will throw (other than, potentially, the hash or comparison functions).  
* If movement may throw but the element is copyable, copy construction is used; in the event of an exception while copying, the new bucket array is discarded and the old one remains valid (its elements haven't been modified).  
* Otherwise (elements not copyable and movement may throw), we're out of luck and we use move construction, but the exception safety guarantee can't be held.  
  
This is the same approach that `std::vector`, for instance, follows for resizing.

---

## Comment 7

> Username: joaquintides  
> Created at: 2024-09-15 10:25:44 UTC  
> Url: https://github.com/boostorg/unordered/issues/282#issuecomment-2351521247  

@kantan2015 you may want to verify that [this PR](https://github.com/boostorg/flyweight/pull/16) fixes your problem.

---

## Comment 8

> Username: kantan2015  
> Created at: 2024-09-15 10:29:01 UTC  
> Url: https://github.com/boostorg/unordered/issues/282#issuecomment-2351523565  

@joaquintides   
Thank you for PR for improvement and the explanation.
