# #329 - [Feature Request] --> unordered_node_map::erase(pointer) [Probably not possible] [Closed]

> Username: nbooster  
> Created at: 2025-12-07 09:52:35 UTC  
> Updated at: 2026-01-06 11:53:43 UTC  
> Closed at: 2026-01-06 11:53:29 UTC  
> Url: https://github.com/boostorg/unordered/issues/329  

Hello,  
  
probably it is not possible,  
but I was wondering if there can be a way  
to erase a node from a boost::unordered_node_map  
by passing the pointer to it (since iterators are not stable),  
and avoid recomputing the hash by calling erase(key)...  
  
Or maybe if we could get an iterator from a pointer   
(something similar to what intrusive containers have)....  
  
The idea is to find an item (key) in the container,  
store it somewhere (through it's pointer, since only that is stable),  
add, edit and remove other items in the container,  
and then decide to erase that item,  
without having to recompute the hash (we did it once).  
  
We basically save one hash computation every time we want to erase an already found item,   
and the container has changed, but our item hasn't...  
  
Does this seem like a good idea to anyone (even if it is not possible) ?

---

## Comment 1

> Username: joaquintides  
> Created at: 2026-01-01 17:50:59 UTC  
> Updated at: 2026-01-01 17:53:38 UTC  
> Url: https://github.com/boostorg/unordered/issues/329#issuecomment-3703944543  

Hi, it'd be fairly easy to provide a `get_iterator(const_pointer)` function for _flat_ containers (all elements lie in a contiguous memory array so the implementation would only need to do some bounds checking), but not so for node-based maps, as there's no way to get from the node to the core array.  
  
If you absolutely need to have this functionality in your project, some hack like the following would work (with the important limitation that, as iterators are not stable, this'd break the minute a rehash is done):  
  
https://godbolt.org/z/bP1Tqcc8j  
```cpp  
#include <boost/unordered/unordered_node_map.hpp>  
  
template<typename Key, typename T>  
struct value_and_iterator  
{  
  value_and_iterator(const T& value_): value{value_} {}  
    
  T value;  
  typename boost::unordered_node_map<Key, value_and_iterator<Key, T>>::iterator it = {};  
};  
  
template<typename Iterator>  
Iterator save_iterator(Iterator it)  
{  
  it->second.it = it;  
  return it;  
}  
  
#include <iostream>  
#include <memory>  
  
int main()  
{  
  boost::unordered_node_map<int, value_and_iterator<int, int>> m;  
    
  auto p = std::addressof(*(save_iterator(m.emplace(1, 42).first)));  
  auto it = p->second.it;  
  std::cout<< "(" << it->first << ", " << it->second.value << ")\n";  
}  
```

---

## Comment 2

> Username: nbooster  
> Created at: 2026-01-01 19:12:49 UTC  
> Url: https://github.com/boostorg/unordered/issues/329#issuecomment-3704015802  

Thanks for the answer.  
Maybe I was not clear enough.  
An example might help.  
I understand that it is not possible to happen...  
  
https://godbolt.org/z/Y5ja4h796  
  
```cpp  
#include <boost/unordered/unordered_node_map.hpp>  
#include <iostream>  
#include <memory>  
  
/*  
The goal is this:  
  
1) Create a fast hash map.  
2) Insert some initial values and store handles to them.  
3) Modify the map (ex: Insert more values).   
4) Erase one of the initial values through it's stored handle  
   WITHOUT recomputing it's hash.  
5) Access the rest of the initial values through their stored handles.  
  
Notes:  
  
boost::unordered_flat_map --> NOT OK   
(no item stability at all).  
  
boost::unordered_node_map with iterators as handles --> NOT OK   
(erase without recomputing hash (through iterators), but no iterator stability).  
  
boost::unordered_node_map with item pointers as handles --> NOT OK   
(pointer stability, but erase has to recompute the hash through ptr->first).  
  
GOAL:  
Item stabilty and erase NOT recomputing item hash.  
*/  
  
void withIterators(void)  
{  
    boost::unordered_node_map<int, char> Map;  
    
    auto itA = Map.try_emplace(1, 'A').first;  
    auto itB = Map.try_emplace(2, 'B').first;  
  
    for ( int index{3}; index < 50; ++index )  
        Map.try_emplace(index, 'C');  
  
    //Map.erase(itA); // <-- uncomment this and it crashes  
  
    for ( int index{50}; index < 100; ++index )  
        Map.try_emplace(index, 'C');  
  
    //std::cout << itB->first << std::endl; // <-- uncomment this and it crashes  
  
    std::cout << Map.size() << std::endl;  
}  
  
void withPointers(void)  
{  
    boost::unordered_node_map<int, char> Map;  
    
    auto ptrA = std::addressof(*(Map.try_emplace(1, 'A').first));  
    auto ptrB = std::addressof(*(Map.try_emplace(2, 'B').first));  
  
    for ( int index{3}; index < 50; ++index )  
        Map.try_emplace(index, 'C');  
  
    // GOAL: How to do this, wthout having to recompute the hash of the key ?  
    Map.erase(ptrA->first);  
  
    for ( int index{50}; index < 100; ++index )  
        Map.try_emplace(index, 'C');  
  
    std::cout << ptrB->first << std::endl;  
  
    std::cout << Map.size() << std::endl;  
}  
  
int main()  
{  
    // This does not work because of iterator instability (as expected).  
    withIterators();  
  
    // This does work, but when erases an item, it recomputes it's hash.  
    withPointers();  
}  
  
```

---

## Comment 3

> Username: joaquintides  
> Created at: 2026-01-02 09:48:04 UTC  
> Url: https://github.com/boostorg/unordered/issues/329#issuecomment-3704906331  

Ok, caching the hash value is easy enough:  
  
https://godbolt.org/z/qxzjP7a9M  
```cpp  
#include <boost/container_hash/hash.hpp>  
#include <boost/container_hash/hash_is_avalanching.hpp>  
  
template<typename T>  
class cached_hash  
{  
public:  
  template<typename... Args>   
  explicit cached_hash(Args&&... args): x(std::forward<Args>(args)...) {}  
  
  const T& get() const noexcept { return x; }  
  operator const T&() const noexcept { return get(); }    
  const std::size_t& hash() const noexcept { return h; }  
    
private:  
  T           x;  
  std::size_t h = boost::hash<T>{}(x);  
};  
  
template<typename T>  
struct cached_hash_hash  
{  
  using is_transparent = void;  
  using is_avalanching = boost::hash_is_avalanching<boost::hash<T>>;  
    
  std::size_t operator()(const cached_hash<T>& x) const noexcept  
  {  
    return x.hash();  
  }  
  
  std::size_t operator()(const T& x) const noexcept  
  {  
    return boost::hash<T>()(x);  
  }  
};  
  
template<typename T>  
struct cached_hash_equal_to  
{  
  using is_transparent = void;  
    
  bool operator()(const cached_hash<T>& x, const cached_hash<T>& y) const  
  {  
    return x.hash() == y.hash() && x.get() == y.get();  
  }  
        
  bool operator()(const cached_hash<T>& x, const T& y) const  
  {  
    return x.get() == y;  
  }  
  
  bool operator()(const T& x, const cached_hash<T>& y) const  
  {  
    return x == y.get();  
  }  
};  
  
#include <boost/unordered/unordered_node_map.hpp>  
#include <iostream>  
#include <string>  
  
int main()  
{  
    using cached_hash_string =  cached_hash<std::string>;  
    boost::unordered_node_map<  
      cached_hash_string, char,   
      cached_hash_hash<std::string>, cached_hash_equal_to<std::string>> Map;  
    
    auto ptrA = std::addressof(*(Map.try_emplace(cached_hash_string("hello"), 'A').first));  
    auto ptrB = std::addressof(*(Map.try_emplace(cached_hash_string("goodbye"), 'B').first));  
  
    Map.reserve(1000);  
    Map.erase(ptrA->first);  
  
    std::cout << ptrB->first.get() << std::endl;  
    std::cout << Map.find("goodbye")->first.get() << std::endl;  
  
    std::cout << Map.size() << std::endl;    
}  
  
```

---

## Comment 4

> Username: nbooster  
> Created at: 2026-01-05 15:51:57 UTC  
> Url: https://github.com/boostorg/unordered/issues/329#issuecomment-3710996870  

Thanks for the answer.  
In short terms it is not possible   
(maybe in the future or another implementation).  
Although it's a nice technique for expensive hash functions or complicated inputs,  
caching does not help in my case   
(I have tried it before, and also tried your technique just for fun   
[I also removed the hash check in the value equality comparison, for more speed]).  
It slowed my program by a lot, as expected.  
I suspect that this happened, mainly, because it increased the memory (cache) footprint  
(my program has lots of hash maps, with a few simple int32_t values as keys each one,   
and I assume that boost::hash<int32_t> is just a modulo operation or something similar,  
and because it's an ULL application,   
I was wondering if it was possible to avoid even that operation).  
Thanks again.  
*You can close this if you like, or leave it open for future reference.

---

## Comment 5

> Username: joaquintides  
> Created at: 2026-01-06 11:53:43 UTC  
> Url: https://github.com/boostorg/unordered/issues/329#issuecomment-3714427798  

Good luck with your project
