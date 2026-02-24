# #50 - [Question] Calling modify(iterator position,Modifier mod) with no key updating from shared lock is safe ? [Closed]

> Username: redboltz  
> Created at: 2021-08-31 05:55:28 UTC  
> Updated at: 2021-08-31 07:58:40 UTC  
> Closed at: 2021-08-31 07:44:43 UTC  
> Url: https://github.com/boostorg/multi_index/issues/50  

Here are a class and a multi_index contaier.  
  
```cpp  
struct foo {  
    int key1;  
    int key2;  
    std::atomic<int> no_key; // thread-safe itself  
};  
  
namespace mi = boost::multi_index;  
using mi_foo = mi::multi_index_container<  
    foo,  
    mi::indexed_by<  
        mi::ordered_non_unique<  
            mi::key<&foo::key1>  
        >,  
        mi::ordered_non_unique<  
            mi::key<&foo::key2>  
        >  
    >  
>;  
```  
  
key1 and key2 are used as index key but no_key doesn't.  
In order to access the container, I use `boost::shared_mutex`.  
  
```cpp  
    boost::shared_mutex m;  
    mi_foo mf;  
```  
  
When I insert to mf or erase from mf, I use exclusive lock.  
  
```cpp  
    boost::lock_guard<boost::shared_mutex> g{m}; // exclusive lock guard  
    mf.emplace(....);  
````  
  
When I want to update no_key, I can't do as follows:  
  
```cpp  
    boost::shared_lock_guard<boost::shared_mutex> g{m}; // shared lock guard  
    auto it = mf.begin(); // in actual code, find something  
    ++it->no_key;          
```  
  
It's because it is const_iterator. It is reasonable that if key1 or key2 is overwritten, indexes would be broken.  
  
So I need to do as follows:  
  
```cpp  
    boost::shared_lock_guard<boost::shared_mutex> g{m}; // shared lock guard  
    auto it = mf.begin(); // in actual code, find something  
    mf.modify(  
        it,  
        [](auto& e) {  
            ++e.no_key;  
        }  
    );  
```  
  
After mod lambda expression is called, multi_index container can re-calculate indexes.  
  
modify() is called is **shared** lock guard. That means multiple threads can call modify() concurrently.  
Is it safe as long as I update only no key members in modify() function from multiple threads ?  
NOTE no_key itself is thread safe because `std::atomic<int> no_key`.  
  
I'm not sure after mod function called process is thread safe if keys are not updated.

---

## Comment 1

> Username: joaquintides  
> Created at: 2021-08-31 07:29:22 UTC  
> Updated at: 2021-08-31 07:58:40 UTC  
> Url: https://github.com/boostorg/multi_index/issues/50#issuecomment-908974687  

There's two ways to answer this:  
  
* Official answer: Boost.MultiIndex abides by standard library requirements on data race avoidance, and in particular guarantees [[res.on.data.races]/3](http://eel.is/c++draft/res.on.data.races#3), by which concurrent access to a container through const functions is safe. As `modify` is not const, your scenario is not safe.  
* Unofficial answer: if you're not modifying any key **and no user-specified object (compare object, hasher, etc.) throws**, `modify` does not actually modify anything (other than the element itself). So, yes, the scenario is unofficially safe. In this case, though, I think it's more informative and clear to just `const_cast` the element and avoid using `modify`.

---

## Comment 2

> Username: redboltz  
> Created at: 2021-08-31 07:44:43 UTC  
> Url: https://github.com/boostorg/multi_index/issues/50#issuecomment-908984001  

Thank you for prompt response.  
  
As you mentioned, `const_cast` is the best choice for this situation.  
  
So I will update as follows:  
  
## before  
```cpp  
    mf.modify(  
        it,  
        [](auto& e) {  
            ++e.no_key;  
        }  
    );  
```  
## after  
```cpp  
   ++const_cast<std::atomic<int>&>(it->no_key);  
```
