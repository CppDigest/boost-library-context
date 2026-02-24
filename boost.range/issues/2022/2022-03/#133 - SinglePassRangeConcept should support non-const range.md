# #133 - SinglePassRangeConcept should support non-const range [Open]

> Username: grtowel1510f  
> Created at: 2022-03-19 12:39:33 UTC  
> Updated at: 2022-03-19 14:46:29 UTC  
> Url: https://github.com/boostorg/range/issues/133  

A single pass range often can be consumed only once which implies mutability and negates constness, on the first consumption it becomes empty. SinglePassRangeConcept asserts [`const_constraints`](https://github.com/boostorg/range/blob/d6e0a32f2ee3094bbe237769d2dd8e4cb81f8bf6/include/boost/range/concepts.hpp#L299-L306) which is a requirement that cannot be met by many or most single pass ranges.  
  
I will demonstrate using a boost coroutine with a range adaptor:  
```cpp  
#include <boost/range/adaptor/transformed.hpp>  
#include <boost/coroutine2/coroutine.hpp>  
  
using boost::adaptors::transformed;  
using boost::coroutines2::coroutine;  
  
int main() {  
    using generator = coroutine<int>;  
    generator::pull_type g([](auto &yield) {  
        for (int i = 0; i < 10; i++) {  
            yield(i);  
        }  
    });  
  
    for (auto i: g | transformed([](int i) { return i + 10; })) {  
    }  
  
    return 0;  
}  
```  
  
This code snippet does not compile because SinglePassRangeConcept asserts `const_constraints` which boost coroutine does not satisfy.  
  
I propose to drop the const requirement for SinglePassRangeConcept because in simple terms, a single pass range isn't required to have a const iterator.

---

## Comment 1

> Username: grtowel1510f  
> Created at: 2022-03-19 12:45:44 UTC  
> Updated at: 2022-03-19 14:46:13 UTC  
> Url: https://github.com/boostorg/range/issues/133#issuecomment-1073003702  

This is a reduction of the previous coroutine demonstration using a simplified generator class to realize a better controlled experiment:  
```cpp  
#include <iterator>  
#include <boost/range/adaptor/transformed.hpp>  
  
class generator {  
public:  
    int i = 0;  
    int& value() { return i; }  
    void next() { i++; }  
    bool empty() const { return i == 10; }  
  
    class iterator {  
    public:  
        using iterator_category = std::input_iterator_tag;  
        using iterator_concept = std::input_iterator_tag;  
        using difference_type = ptrdiff_t;  
        using value_type = int;  
        using pointer = int*;  
        using reference = int&;  
  
        generator *g;  
  
        reference operator*() const { return g->value(); }  
        iterator& operator++() {  
            g->next();  
            return *this;  
        }  
        inline iterator& operator++(int) { return operator++(); }  
        bool operator==(const iterator& other) const {  
            if (other.g == nullptr) {  
                return !g || g->empty();  
            }  
            return g == other.g;  
        }  
  
        bool operator!=(const iterator& other) const { return !operator==(other); }  
  
        iterator(generator& g) : g(&g) {}  
        iterator() = default;  
    };  
  
    iterator begin() {  
        return iterator(*this);  
    }  
  
    iterator end() {  
        return iterator();  
    }  
  
};  
  
using boost::adaptors::transformed;  
  
int main() {  
    generator g;  
    for (auto i : g | transformed([](int i) { return i + 10; })) {  
    }  
  
    return 0;  
}  
```
