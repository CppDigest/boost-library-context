# #27 - [Feature request] get element node function for moving an element from the container [Closed]

> Username: redboltz  
> Created at: 2019-08-03 12:06:03 UTC  
> Updated at: 2021-08-20 06:58:13 UTC  
> Closed at: 2021-08-20 06:58:13 UTC  
> Url: https://github.com/boostorg/multi_index/issues/27  

Since C++17, associative containers of STL such as `std::set` have a member function `extract()`.  
It returns internal `node-handle`.  
  
https://en.cppreference.com/w/cpp/container/set/extract  
https://en.cppreference.com/w/cpp/container/node_handle  
  
If multi_index's containers have the same or similar member function, it is useful especially moving an element from the container. I tried to do that using `modify()` member function but I couldn't do that. I wrote a move operation in 2nd argument (lambda expression) of `modify()`. `modify()` calls the lambda expression and then, does re-arrange process. During the process, the container accesses the moved from object.  
  
Here is a discussion on the stackoverflow:  
https://stackoverflow.com/questions/57335172/is-there-any-way-to-move-an-element-from-ordered-index-ed-multi-index  
  
I guess `multi_index` container will be able to have something like `extract()` member function but I'm not sure it is possible.

---

## Comment 1

> Username: joaquintides  
> Created at: 2019-08-05 07:55:59 UTC  
> Url: https://github.com/boostorg/multi_index/issues/27#issuecomment-518127806  

Hi Takatoshi,  
  
As for your original use case, I've just replied with a [solution](https://stackoverflow.com/a/57354248/213114) that hopefully fulfills your needs. With this in mind, I don't find justification for accepting #28, which introduces a novel interface without analogous in the standard library.   
  
As for adding `extract` following C++17, well, yes, this is totally doable and something I can consider implementing when time permits. Of course, PRs along that direction are most welcome.

---

## Comment 2

> Username: redboltz  
> Created at: 2019-08-05 09:13:00 UTC  
> Url: https://github.com/boostorg/multi_index/issues/27#issuecomment-518153485  

Thank you! I'm surprised at your solution but I understand it. The element is erased at https://github.com/boostorg/multi_index/blob/develop/include/boost/multi_index_container.hpp#L847  
The solution is based on intentional exception throwing and catching. It is enough as a temporary solution for me. But I think that we should have more efficient and easy to understand solution in the future.   
  
> As for your original use case, I've just replied with a solution that hopefully fulfills your needs. With this in mind, I don't find justification for accepting #28, which introduces a novel interface without analogous in the standard library.  
  
Fair enough. I understand your design policy.

---

## Comment 3

> Username: joaquintides  
> Created at: 2019-08-05 09:21:43 UTC  
> Url: https://github.com/boostorg/multi_index/issues/27#issuecomment-518156542  

> The solution is based on intentional exception throwing and catching. It is enough as a temporary solution for me. But I think that we should have more efficient and easy to understand solution in the future.  
  
Absolutely. Implementing standard `extract` is really the way to go.

---

## Comment 4

> Username: jonesmz  
> Created at: 2019-09-02 20:31:47 UTC  
> Updated at: 2019-09-02 20:34:03 UTC  
> Url: https://github.com/boostorg/multi_index/issues/27#issuecomment-527238664  

Please consider the possible use case of two multi-index containers which store the same underlying structure. For example, the `session_state` class, and the two containers (typedefs) `mi_active_sessions` and `mi_non_active_sessions` code : https://github.com/redboltz/mqtt_cpp/blob/e5b0f41e9f57729edbdf671c069614d281d4914c/test/test_broker.hpp#L1000  
  
When a session goes from active to non-active, we wish to extract the value from the `mi_active_sessions` container, modify one the member variables from the extracted object, which happens to be a key for `mi_active_sessions`, but not for `mi_non_active_sessions`, (in this case, set the `std::shared_ptr`, `con`, to `nullptr`), and then insert the resulting object into the `mi_non_active_sessions` container. It would be wonderful if, for boost multi-index containers where the underlying storage is compatible, that operation could be done without allocating any storage, or copying the stored-object, similar to how it's possible with `std::list` with the splice function currently.  
  
Of course, this is only one example, and there's no real problem with solving the task with the solution you proposed above. Just wanted to point this out as one of the situations that motivate the request to support an `extract` function.

---

## Comment 5

> Username: joaquintides  
> Created at: 2020-05-10 16:44:17 UTC  
> Updated at: 2020-05-10 17:43:04 UTC  
> Url: https://github.com/boostorg/multi_index/issues/27#issuecomment-626355728  

Takatoshi, Michael,  
  
I've just implemented node extraction and insertion. You can try it out by pulling the develop branch or downloading from [here](https://github.com/boostorg/multi_index/archive/1b1e29d6e324fbf55b5ee0080aa8fcd7d2f46197.zip).  
  
I'm leaving this issue open as I may implement `merge` operations as well in the following weeks.

---

## Comment 6

> Username: redboltz  
> Created at: 2020-05-11 03:39:45 UTC  
> Url: https://github.com/boostorg/multi_index/issues/27#issuecomment-626453127  

@joaquintides , thank you very much!!  
  
I downloaded the multi-index from the link you mentioned and wrote the following code:  
  
```cpp  
#include <iostream>  
  
#include "boost/multi_index_container.hpp"  
#include "boost/multi_index/ordered_index.hpp"  
#include "boost/multi_index/identity.hpp"  
  
struct trace {  
    trace() {  
        std::cout << this << ":" << " construct" << std::endl;  
    }  
    ~trace() {  
        std::cout << this << ":" << " destruct" << std::endl;  
    }  
    trace(trace& other) {  
        std::cout << this << ":" << " copy construct from " << &other << std::endl;  
    }  
    trace(trace&& other) {  
        std::cout << this << ":" << " move construct from " << &other << std::endl;  
    }  
    trace& operator=(trace const& other) {  
        std::cout << this << ":" << " copy assign from    " << &other << std::endl;  
        return *this;  
    }  
    trace& operator=(trace&& other) {  
        std::cout << this << ":" << " move assign from    " << &other << std::endl;  
        return *this;  
    }  
};  
  
inline bool operator<(trace const& lhs, trace const& rhs) {  
    std::cout << &lhs << " < " << &rhs << std::endl;  
    return &lhs < &rhs;  
}  
  
namespace mi = boost::multi_index;  
  
using mi_trace = mi::multi_index_container<  
    trace,  
    mi::indexed_by<  
        mi::ordered_unique<  
            mi::identity<trace>  
        >  
    >  
>;  
  
int main () {  
    mi_trace mi;  
  
    mi.insert(trace());  
    mi.insert(trace());  
    mi.insert(trace());  
  
    auto it = mi.begin();  
    ++it;  
  
    assert(mi.size() == 3);  
    std::cout << "[[extract and move]]" << std::endl;  
    trace target = std::move(mi.extract(it).value());  
    std::cout << "[[erase]]" << std::endl;  
    assert(mi.size() == 2);  
}  
```  
  
It works perfectly.

---

## Comment 7

> Username: joaquintides  
> Created at: 2021-08-19 10:15:13 UTC  
> Updated at: 2021-08-19 15:10:30 UTC  
> Url: https://github.com/boostorg/multi_index/issues/27#issuecomment-901790763  

Hi Takatoshi, Michael,  
  
I completed the job and implemented `merge` (and extended `splice` accordingly), see  a52810fc3d246384ca8a74d0f9676bc58e216735:  
  
* Added `merge` operations to key-based indices. The functionality goes beyond the standard specification for (unordered) associative containers in a number of ways, most notably:  
  * The source index can be of any type, including non key-based indices.  
  * Partial merge is provided: for instance, `x.merge(y,first,last)` merges only the elements of y within [`first`,`last`).  
* Previous versions of `splice` for sequenced and random access indices were destructive, i.e. elements were copy-inserted into the destination and then erased from the source. Now, `splice` is based on node transfer much as `merge` in key-based indices, and has been similarly extended to accept source indices of any type: in fact, `splice` can be regarded as a frontend to the same functionality provided by `merge` in key-based indices. For reasons of backwards compatibility, the destructive behavior of `splice` has been retained in the case that the source and destination containers have unequal allocators.  
* The fact has been documented that index iterator types do only depend on `node_type`, (except for hashed indices, where uniqueness/non-uniqueness is also a dependency). This has implications on the validity of iterators to elements transferred by `merge` or `splice`. This property is a variant of what has been called [SCARY iterators](https://wg21.link/n2980) in the C++ standard mailing lists. SCARYness is currently (August 2021) not mandated for standard containers.  
* Iterator SCARYness is now also preserved in [safe mode](https://www.boost.org/libs/multi_index/doc/tutorial/debug.html#safe_mode).   
  
I'd be great if you could play a little with this new functionality and report anything strange you see.

---

## Comment 8

> Username: redboltz  
> Created at: 2021-08-20 05:52:58 UTC  
> Url: https://github.com/boostorg/multi_index/issues/27#issuecomment-902449917  

@joaquintides , I played with merge functionality using the following code. As long as I used, everything works fine!  
  
```cpp  
#include <iostream>  
  
#include "boost/multi_index_container.hpp"  
#include "boost/multi_index/ordered_index.hpp"  
#include "boost/multi_index/identity.hpp"  
#include "boost/multi_index/sequenced_index.hpp"  
  
struct trace {  
    trace(int id):id{id} {  
        std::cout << id << " " << this << ":" << " construct" << std::endl;  
    }  
    ~trace() {  
        std::cout << id << " " << this << ":" << " destruct";  
        if (moved) std::cout << "[moved]";  
        std::cout << std::endl;  
    }  
    trace(trace& other):id{other.id} {  
        std::cout << id << " " << this << ":" << " copy construct from " << &other << std::endl;  
        assert(false);  
    }  
    trace(trace&& other):id{other.id} {  
        id = other.id;  
        other.moved = true;  
        std::cout << id << " " << this << ":" << " move construct from " << &other << std::endl;  
    }  
    trace& operator=(trace const& other) {  
        id = other.id;  
        std::cout << id << " " << this << ":" << " copy assign from    " << &other << std::endl;  
        assert(false);  
        return *this;  
    }  
    trace& operator=(trace&& other) {  
        other.moved = true;  
        std::cout << id << " " << this << ":" << " move assign from    " << &other << std::endl;  
        return *this;  
    }  
    void print() const {  
        std::cout << id << " " << this << std::endl;  
    }  
    int id;  
    bool moved = false;  
};  
  
inline bool operator<(trace const& lhs, trace const& rhs) {  
    return lhs.id < rhs.id;  
}  
  
namespace mi = boost::multi_index;  
  
using mi_trace = mi::multi_index_container<  
    trace,  
    mi::indexed_by<  
        mi::ordered_unique<  
            mi::identity<trace>  
        >  
    >  
>;  
  
// target empty  
void test1() {  
    std::cout << "test1" << std::endl;  
    mi_trace mi1;  
    mi_trace mi2;  
  
    mi1.insert(trace(1));  
    mi1.insert(trace(2));  
    mi1.insert(trace(3));  
  
    mi2.merge(mi1);  
  
    assert(mi1.empty());  
    for (auto const& e : mi2) e.print();  
}  
  
// target has eleemnts  
void test2() {  
    std::cout << "test2" << std::endl;  
    mi_trace mi1;  
    mi_trace mi2;  
  
    mi1.insert(trace(5));  
    mi1.insert(trace(1));  
    mi1.insert(trace(3));  
  
    mi2.insert(trace(4));  
    mi2.insert(trace(2));  
  
    mi2.merge(mi1);  
  
    assert(mi1.empty());  
    for (auto const& e : mi2) e.print();  
}  
  
// target has conflict element  
void test3() {  
    std::cout << "test3" << std::endl;  
    mi_trace mi1;  
    mi_trace mi2;  
  
    mi1.insert(trace(5));  
    mi1.insert(trace(1));  
    mi1.insert(trace(3));  
  
    mi2.insert(trace(4));  
    mi2.insert(trace(3));  
  
    mi2.merge(mi1);  
  
    assert(mi1.size() == 1);  
    assert(mi1.begin()->id == 3);  
    for (auto const& e : mi2) e.print();  
}  
  
namespace mi = boost::multi_index;  
  
struct tag_id {};  
struct tag_seq {};  
  
using mi_trace2 = mi::multi_index_container<  
    trace,  
    mi::indexed_by<  
        mi::ordered_unique<  
            mi::tag<tag_id>,  
            mi::identity<trace>  
        >,  
        mi::sequenced<  
            mi::tag<tag_seq>  
        >  
    >  
>;  
  
// also has sequenced index  
void test4() {  
    std::cout << "test4" << std::endl;  
    mi_trace2 mi1;  
    mi_trace2 mi2;  
  
    mi1.insert(trace(5));  
    mi1.insert(trace(1));  
    mi1.insert(trace(3));  
  
    mi2.insert(trace(4));  
    mi2.insert(trace(3));  
    mi2.insert(trace(2));  
  
    mi2.merge(mi1);  
  
    assert(mi1.size() == 1);  
    assert(mi1.begin()->id == 3);  
    for (auto const& e : mi2) e.print();  
    std::cout << "seq" << std::endl;  
    for (auto const& e : mi2.get<tag_seq>()) e.print();  
}  
  
// partial merge  
void test5() {  
    std::cout << "test5" << std::endl;  
    mi_trace mi1;  
    mi_trace mi2;  
  
    mi1.insert(trace(5));  
    mi1.insert(trace(1));  
    mi1.insert(trace(3));  
  
    mi2.insert(trace(4));  
    mi2.insert(trace(2));  
  
    mi2.merge(mi1, mi1.cbegin(), ++++mi1.cbegin());  
  
    assert(mi1.size() == 1);  
    assert(mi1.begin()->id == 5);  
    for (auto const& e : mi2) e.print();  
}  
  
int main () {  
    test1();  
    test2();  
    test3();  
    test4();  
    test5();  
}  
```
