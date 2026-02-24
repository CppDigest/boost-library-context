# #100 - sizeof(slist) depends on its value_traits, which is not intuitive [Closed]

> Username: RFoe  
> Created at: 2025-11-03 06:56:44 UTC  
> Updated at: 2025-11-03 09:55:21 UTC  
> Closed at: 2025-11-03 07:43:27 UTC  
> Url: https://github.com/boostorg/intrusive/issues/100  

is this a bug or just my misusage  
  
env:   
Ubuntu clang++ version 21.1.5  
C++26  
-stdlib=libc++  
boost-intrusive-1.88(downloaded by vcpkg)  
  
```cpp  
namespace bi = boost::intrusive;  
  
struct tag {};  
  
struct alignas(void *) value_node {  
    value_node *next_;  
    alignas(void *) unsigned char dummy[sizeof(void *) * 10]; // extra data  
};  
  
struct node_traits {  
    using node           = value_node;  
    using node_ptr       = value_node *;  
    using const_node_ptr = const value_node *;  
  
    static auto get_next(const node *n) noexcept -> node * { return n->next_; }  
    void        set_next(node *n, node *next) noexcept { n->next_ = next; }  
};  
  
template <typename Ty>  
using derivation_value_traits =  
    bi::derivation_value_traits<Ty, node_traits, bi::normal_link>;  
  
struct special_value : public value_node {}; // our value type  
  
template <typename Ty>  
using list = bi::slist<  
    Ty, bi::tag<tag>, bi::value_traits<derivation_value_traits<Ty>>,  
    bi::constant_time_size<false>, bi::cache_last<false>, bi::linear<false>>;  
  
static_assert(__is_empty(derivation_value_traits<int>));  
  
using special_value_list = list<special_value>;  
  
auto main() -> int {  
  
    static_assert(alignof(special_value_list) == alignof(void *));  
    static_assert(sizeof(special_value_list) == sizeof(void *));  
  
/*  
  
Static assertion failed due to requirement 'sizeof(boost::intrusive::slist<special_value, boost::intrusive::tag<tag>, boost::intrusive::value_traits<boost::intrusive::derivation_value_traits<special_value, node_traits, boost::intrusive::normal_link>>, boost::intrusive::constant_time_size<false>, boost::intrusive::cache_last<false>, boost::intrusive::linear<false>>) == sizeof(void *) * 2'clang(static_assert_requirement_failed)  
main.cxx(43, 19): Expression evaluates to '88 == 16'  
  
*/  
}  
  
  
```  
  
I see the issue. using -Xclang   -fdump-record-layouts  
  
i can see that slist contains a node data member(instead of node_pointer) by inheritance.  
(slist <- slist_impl <-data_t <-root_plus_size <- header_holder_plus_last <- default_header_holder <- node data member)  
  
why using dummy sentinel algorithm?  
  
what if the node is list is 4K memory page? that makes the sizeof(list) itself 4k bytes, even when the list is still empty yet.  
  
And Unpredictable size is awful, in embeded senarios or template programming senarios.  
  
And that makes recursive definition is impossible since sizeof(slist/list) is not constant:  
  
  
```cpp  
  
struct node_t {  
      node_t* next_;  
      alignas(void*) unsigned char self_container[sizeof(void*)];  
};  
  
```  
  
another drawback is that default constructor of node type is required, which is not necessarily needed.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-11-03 07:43:27 UTC  
> Url: https://github.com/boostorg/intrusive/issues/100#issuecomment-3479274385  

I think "set_next" should be "static".  
  
All node containers must contain a stable "end" node to guarantee iterator invalidation rules, traditional STL implementations either allocate it (which Intrusive can't) or store it internally. Storing it internally guarantees nothrow default and move constructors (since the allocation can't fail).  
  
Since you've declared "node_traits::node" as "value_node" and since slist must store a node internally to represent the "end node", its size can't be sizeof(void*). Having different types for node and value also allows sharing slist algorithm instantiations (which only depende on the type of node) between different slists holding different value types.  
  
You are using "derivation_value_traits" which precisely sets the relationship between the node and the value to  well, derivation. Since you need a small slist, define a minimal "node" type and derive "value_node" (bigger type) from that:  
  
Try the following:  
  
```  
#include <boost/intrusive/slist.hpp>  
#include <boost/intrusive/derivation_value_traits.hpp>  
  
namespace bi = boost::intrusive;  
  
struct tag {};  
  
struct alignas(void *) slist_node {  
    slist_node *next_;  
};  
  
struct alignas(void *) value_node : slist_node {  
    alignas(void *) unsigned char dummy[sizeof(void *) * 10]; // extra data  
};  
  
struct node_traits {  
    using node           = slist_node;  
    using node_ptr       = slist_node *;  
    using const_node_ptr = const slist_node *;  
  
    static auto get_next(const node *n) noexcept -> node * { return n->next_; }  
    static void set_next(node *n, node *next) noexcept { n->next_ = next; }  
};  
  
template <typename Ty>  
using derivation_value_traits =  
    bi::derivation_value_traits<Ty, node_traits, bi::normal_link>;  
  
struct special_value : public value_node {}; // our value type  
  
template <typename Ty>  
using list = bi::slist<  
    Ty, bi::tag<tag>, bi::value_traits<derivation_value_traits<Ty>>,  
    bi::constant_time_size<false>, bi::cache_last<false>, bi::linear<false>>;  
  
using special_value_list = list<special_value>;  
  
auto main() -> int {  
  
   special_value_list l;  
   static_assert(alignof(special_value_list) == alignof(void *));  
   static_assert(sizeof(special_value_list) == sizeof(void *));  
   return 0;  
}  
```
