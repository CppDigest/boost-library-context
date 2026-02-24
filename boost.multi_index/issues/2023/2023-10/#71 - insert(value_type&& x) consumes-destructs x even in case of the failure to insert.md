# #71 - [MultiIndex] insert(value_type&& x) consumes/destructs x even in case of the failure to insert [Closed]

> Username: AndyVenikov  
> Created at: 2023-10-14 14:47:38 UTC  
> Updated at: 2023-10-21 00:36:58 UTC  
> Closed at: 2023-10-15 15:35:46 UTC  
> Url: https://github.com/boostorg/multi_index/issues/71  

If we want to follow std::set::insert, then insert should have no effect if insertion did not take place. Otherwise, insert is unusable in cases when collisions are expected as loosing the input argument is an unlikely intent by the caller.  
  
Reproduction:  
https://godbolt.org/z/bofeMo8fd  
```  
#include <memory>  
#include <vector>  
  
#include <boost/multi_index/hashed_index.hpp>  
#include <boost/multi_index/identity.hpp>  
#include <boost/multi_index/indexed_by.hpp>  
#include <boost/multi_index/mem_fun.hpp>  
#include <boost/multi_index/sequenced_index.hpp>  
#include <boost/multi_index/tag.hpp>  
#include <boost/multi_index_container.hpp>  
  
  
  
class Base {  
  public:  
    virtual size_t GetId() const = 0;  
    virtual ~Base() = default;  
};  
  
class Derived : public Base {  
  public:  
    size_t GetId() const { return 42; }  
};  
  
  
int main(int, char**) {  
    // A tag to view elements in the order of insertion.  
    struct Sequenced{};  
    // A tag to view elements in the order sorted by type id.  
    struct Ordered{};  
  
    using ContainerType = boost::multi_index_container<  
        // Elements are pointers to Base.  
        std::unique_ptr<Base>,  
  
        boost::multi_index::indexed_by<  
            boost::multi_index::sequenced<boost::multi_index::tag<Sequenced>>,  
            boost::multi_index::hashed_unique<  
              boost::multi_index::tag<Ordered>,  
              boost::multi_index::const_mem_fun<Base, size_t,  
                                                &Base::GetId>>>>;  
  
    ContainerType container;  
  
    // Insert first element.  
    auto& ordered_view = container.get<Ordered>();  
    auto new_element = std::make_unique<Derived>();  
    auto insert_result = ordered_view.insert(std::move(new_element));  
    if (!insert_result.second) return -1;  
  
    // Try toInsert second element with the same key.  
    auto new_element2 = std::make_unique<Derived>();  
    auto insert_result2 = ordered_view.insert(std::move(new_element2));  
    assert(!insert_result2.second);  
    assert(new_element2->GetId() == 42);  
}  
```

---

## Comment 1

> Username: joaquintides  
> Created at: 2023-10-15 15:35:46 UTC  
> Url: https://github.com/boostorg/multi_index/issues/71#issuecomment-1763426223  

Hi,  
  
Boost.MultiIndex insertion does indeed _not_ modify the `value_type&&` passed if insertion is unsuccessful. The problem with this line  
  
```cpp  
ordered_view.insert(std::move(new_element))  
```  
is that `insert`  expects a `std::unique_ptr<Base>&&` and you're passing a `std::unique_ptr<Derived>&&`: the way `std::unique_ptr`'s implicit construction works, this creates a temporary `std::unique_ptr<Base>&&` to which the value of  
`new_element` is transferred. All of this happens before Boost.MultiIndex is even called, and the net result is that `new_element` will become empty regardless of whether insertion is successful or not.  
  
One possible user-side workaround is to use something like this:  
  
```cpp  
template<typename Container, typename Derived>  
auto insert_unique_ptr(Container& c, std::unique_ptr<Derived>&& p)  
{  
  typename Container::value_type pb = std::move(p);  
  auto res = c.insert(std::move(pb));  
  p.reset(static_cast<Derived*>(pb.release()));  
  return res;  
}  
```  
(https://godbolt.org/z/P5KW6q585)  
  
Closing this issue as a non-bug.

---

## Comment 2

> Username: AndyVenikov  
> Created at: 2023-10-21 00:36:57 UTC  
> Url: https://github.com/boostorg/multi_index/issues/71#issuecomment-1773558415  

I completely missed the implicit conversion required here.  
Thank you very much for point this out!  
And sorry for the noise.
