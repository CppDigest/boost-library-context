# #244 - boost::concurrent_flat_set missing capabilities [Closed]

> Username: jonesmz  
> Created at: 2024-04-30 20:11:30 UTC  
> Updated at: 2025-04-21 18:39:33 UTC  
> Closed at: 2024-06-08 07:18:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/244  

I have a pre-existing data structure in my codebase which is a hand-rolled concurrent hash set. I'd like to replace it with `boost::concurrent_flat_set` but am running into some missing capabilities.  
  
The structure in question is used as a global cache of objects, with reference counting semantics for the items in the cache. When the first instance of a particular value is asked for from the cache, it's constructed and inserted into the set. Subsequent lookups return a new smart pointer to the existing item. When the smart pointer(s) are destructed, the internal reference count on the item in the cache is decreased, and the item is removed when the reference count reaches zero.  
  
Of course, `boost::concurrent_flat_set` does not provide address stability for the items held in the set. That's perfectly OK, since I can put the data that needs address stability into a `std::unique_ptr<>` held by the `boost::concurrent_flat_set::value_type`. That raises my first missing capability:  
  
I'd rather defer the allocation of my `std::unique_ptr<>` until after determining if the set has, or doesn't have, an item matching my data's hash / equality. As far as I can tell, there is no way to do this with the current API for insertion or emplacement, since the `insert_***()` and `emplace_***()` functions all construct the object to be inserted before acquiring the lock and then hash the item (see `boost::detail::foa::concurrent_table::unprotected_norehash_emplace_or_visit`). Since I'm providing the hash function, I expected to be able to provide this hash pre-computed (and calculated BEFORE locking) to the `insert_***()` or `emplace_***()` function calls so that construction of the `value_type` is deferred until the set knows the item isn't already present. At least in boost 1.84 (what I have locally), the construction of `value_type` is taking place after the lock is acquired, so deferring the construction until after the container has determined if the value is already present shouldn't introduce a performance penalty.  
  
The next missing functionality is that currently there is no way to visit the value after it is inserted/emplaced without calling `visit()` after the insertion/emplacement finishes. We have only insert/emplace *OR* visit, not insert/emplace *AND* visit.  For my situation, I had wanted to insert/emplace the value, deferring the allocation of that new std::unique_ptr<> until after the lack of a pre-existing item was confirmed, and then passing the newly constructed or pre-existing item to the visitation function. The visitation function would then increment the reference count (regardless of new or existing), and grab the stable pointer held by the std::unique_ptr.  
  
The third missing functionality (or perhaps this is just a documentation gap?) is the ability to visit an existing item and remove it. The current `erase_if()` function doesn't appear to allow the function to modify the item referred to by the key. My intention is to have my smart pointer that points to the cache call `boost::concurrent_flat_set::erase_if` with a function that returns essentially `return 0 == --refcount;` so that the item is only erased on the last refcount being removed.  
  
And forth, like with `insert_***()` or `emplace_***()`, I know my hash, so would have expected the ability to provide that to the `erase_if` function as an optimization, before the lock is acquired.  
  
  
What I'm trying to do would look something like the following:  
  
```  
struct Node  
{  
    Node(value_type val)  
     : m_value(std::make_unique<value_type>(std::move(val))  
    { }  
    RefCount_t                             m_refcount = 1; // non-atomic refcount, starts at 1, incremented when attached to Item struct.  
    std::unique_ptr<value_type> m_pValue; // *m_pValue is the only thing involved in computing the hash or comparing equality.  
};  
  
struct Cache  
{  
    using MySet_t =  boost::concurrent_flat_set<Node, MyHasher<Node>, MyEqual<Node>>;  
    MySet_t m_set;  
  
    struct Item  
    {  
        Item(MySet_t  & rSet,  
             Node & node)  
         : m_rSet(rSet)  
         , m_rValue(*node.m_value.get())  
        {  
            ++node.m_refcount;  
        }  
        ~Item  
        {  
            // TODO: Is this how erase_if() works?  
            m_rSet.erase_if(m_rValue, [&](Node & node) { return 0 == -- node.m_refcount; });  
        }  
  
        value_type const& m_rValue; // always stable in memory, as it's held by a std::unique_ptr, not by the set itself.  
                                                       // consumers of the Cache::Item may not modify the pointed-to item.  
    private:  
        MySet_t  & m_rSet;  
    }  
  
    template<typename VALUE_T>  
    std::shared_ptr<Item> get(VALUE_T && value)  
    {  
        std::shared_ptr<Item> ret;  
        auto emplaceVisit = [&ret, this](Node & node)  
        {  
            // Because boost::concurrent_flat_set doesn't allow insert-and-visit in a single lock  
            // Node ::Node () needs to ensure that the Node starts with a refcount.  
            // This means that the Item isn't responsible for incrementing the refcount.  
           ++node.m_refcount;  
            ret = std::make_shared<Item>(this->m_set, node);  
        };  
        auto queryVisit = [&ret, this](Node & node)  
        {  
            // don't increase the refcount, we only use this lambda if the emplacement *succeeded*.  
            ret = std::make_shared<Item>(this->m_set, node);  
        };  
  
        // TODO: Because we cannot supply our own hash / equality proxy during insertion  
        // the parameter `value` will always be forwarded from even if no item is constructed  
        // and we will always incur the allocation of a new std::unique_ptr<value_type>  
        // even if there is already an item equal to it, and that allocation always happens under lock.  
        if(m_set.emplace_or_visit(std::forward<VALUE_T>(value), emplaceVisit))  
        {  
            // TODO: Because visit is not called if the item is inserted / emplaced, we must call visit after, re-acquiring the lock.  
            // TODO: We've already (maybe?) moved-from the parameter value, so this might be use-after-move.  
            // copying value_type might be prohibitive or disallowed, and moving it may be expensive, so calling visit()  
            // after insert isn't great.  
            m_set.visit(value, queryVisit);  
        }  
  
        return ret;  
    }  
}  
```  
  
Then the returned `Cache::Item` object will be used by various parts of the code in a read-only fashion.  
  
Typically we use this pattern of caching a value when dealing with long-lived strings coming in from the network. Most of the time, the strings in question are already present in memory either from previous requests or just because they're baked into the code. So we do a one-time lookup and get a globally unique instance of the string.   
  
In many parts of our code, we skip comparing the string value itself, and instead just compare the address of `Cache::Item::m_rValue` to determine equality.  
  
Another aspect of the  
  
1. lock  
2. insert  
3. unlock  
4. lock  
5. visit  
6. unlock  
   
behavior that we need to have to refer to the item just inserted, is that between then the item is inserted and then visited, another thread may visit the item, increase the refcount, and then the smart pointer could be destructed and the refcount dropped to zero and the item in the set removed before the visit operation finishes.   
  
This can be accounted for by starting the refcount at 1, but it's awkward to do.

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-05-02 17:15:06 UTC  
> Updated at: 2024-05-02 17:17:16 UTC  
> Url: https://github.com/boostorg/unordered/issues/244#issuecomment-2091099578  

**Constructing the value_type object only when needed**  
  
This can be done using transparent lookup and some ingenuity (https://godbolt.org/z/5qv6heMvc):  
  
```cpp  
#include <boost/unordered/concurrent_flat_set.hpp>  
#include <iostream>  
#include <memory>  
  
template <typename T>  
struct pre_unique_ptr  
{  
  pre_unique_ptr(const T& x_):  
    x{x_},h{boost::hash<T>()(x)}{}  
  
  operator std::unique_ptr<T>()const  
  {  
    std::cout<<"unique_ptr created\n";  
    return std::unique_ptr<T>{new T(x)};  
  }  
  
  T           x;  
  std::size_t h;  
};  
  
template <typename T>  
struct pre_unique_ptr_hash  
{  
  using is_transparent=void;  
  
  std::size_t operator()(const pre_unique_ptr<T>& x)const  
  {  
    std::cout<<"cached hash returned\n";  
    return x.h;  
  }  
  
  std::size_t operator()(const std::unique_ptr<T>& x)const  
  {  
    std::cout<<"calculated hash returned\n";  
    return boost::hash<T>()(*x);  
  }  
};  
  
template <typename T>  
struct pre_unique_ptr_equal_to  
{  
  using is_transparent=void;  
  
  std::size_t operator()  
    (const pre_unique_ptr<T>& x,const std::unique_ptr<T>& y)const  
    {return x.x==*y;}  
  std::size_t operator()  
    (const std::unique_ptr<T>& x,const pre_unique_ptr<T>& y)const  
    {return *x==y.x;}  
  std::size_t operator()  
    (const std::unique_ptr<T>& x,const std::unique_ptr<T>& y)const  
    {return *x==*y;}  
};  
  
#include <string>  
  
int main()  
{  
  boost::concurrent_flat_set<  
    std::unique_ptr<std::string>,  
    pre_unique_ptr_hash<std::string>,  
    pre_unique_ptr_equal_to<std::string>  
  > s(100);  
  
  std::cout<<"successful insertion\n";  
  s.insert(pre_unique_ptr<std::string>("hello"));  
  
  std::cout<<"unsuccessful insertion\n";  
  s.insert(pre_unique_ptr<std::string>("hello"));  
}  
```  
Output:  
```  
successful insertion  
cached hash returned  
unique_ptr created  
unsuccessful insertion  
cached hash returned  
```  
The idea is to create an insertion object that holds the necessary information (including the cached hash) , interoperates with `value_type` and allows deferred construction by converting to `value_type`.  
  
**Visiting the value after it is inserted**  
  
As you correctly point out, there's no `insert_and_visit`, but you can again use the trick described above sort of like this:  
  
```cpp  
operator std::unique_ptr<T>()const  
{  
  auto p=std::unique_ptr<T>{new T(x)};  
  // do whatever you want with *p just before passing p for final emplacement  
 return p;  
}  
```  
This is admittedly a bit clumsy, as you'll have to use `insert_or_visit` and duplicate the action on the element both here and in the visitation function (when insertion is not succesful).  
  
**Visiting an existing item, modifying it and removing/not removing it**  
  
This is indeed allowed:  
  
```cpp  
  s.erase_if(std::string("hello"),[](const auto& p){  
    // modify the element  
    p->append("!");  
    return p->size()>5; // remove it only if size > 5  
  });  
```  
Note that, in your scenario, we can modify the elements because `s` is a container of `const std::unique_ptr<std::string>`s, so the pointers are const, but the pointed-to values are not. Now, this is _very dangerous_ because you will engender UB if you leave/don't remove a value modified in ways that make its identity and/or hash change, which the snippet above indeed does --if you limit yourself to modifying data members that are logically not part of the key then you're fine, though.  
  
**Providing a precalculated hash to `erase_if`**  
  
This can be done with the same `pre_unique_ptr` trick described before.  
  
I hope this clarifies your doubts. If you finally switch to Boost.Unordered please let me know how the thing fares. Good luck with your project.

---

## Comment 2

> Username: joaquintides  
> Created at: 2025-04-04 10:56:38 UTC  
> Updated at: 2025-04-21 18:39:32 UTC  
> Url: https://github.com/boostorg/unordered/issues/244#issuecomment-2778330502  

**Update 2025/04/04:** After the conversation above, `{emplace|insert}_and_visit` has been added, which solves some of the issues raised in a more elegant manner than the workarounds previously proposed.

---

## Comment 3

> Username: jonesmz  
> Created at: 2025-04-21 15:28:05 UTC  
> Url: https://github.com/boostorg/unordered/issues/244#issuecomment-2818791692  

I'm now using boost::concurrent_node_set, with mutable reference count, in my production environment. Replacing a circa 2010 hand-written hash map implementation with lots of cruft.  
  
Thank you for the excellent open source code.
