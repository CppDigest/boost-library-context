# #88 - boost::transform_iterator does not typedef iterator_category [Closed]

> Username: aeubanks  
> Created at: 2024-10-29 23:06:10 UTC  
> Updated at: 2024-10-30 17:35:28 UTC  
> Closed at: 2024-10-30 10:41:57 UTC  
> Url: https://github.com/boostorg/iterator/issues/88  

https://www.boost.org/doc/libs/1_85_0/libs/iterator/doc/transform_iterator.html says it typedefs `iterator_category` based on the underlying iterator. However, nothing in https://github.com/boostorg/iterator/blob/develop/include/boost/iterator/transform_iterator.hpp seems to indicate that it's doing so.   
  
With https://github.com/llvm/llvm-project/pull/112102 disallowing std::prev(non_cpp17_bidi_iterator), we have code that breaks due to `transform_iterator` not being bidi even though the underlying iterator is.  
  
Example: https://godbolt.org/z/a6TzjGW91  
  
```  
#include <iostream>  
#include <boost/iterator/transform_iterator.hpp>  
  
int main() {  
    auto d = [](int i) { return i * 2; };  
    int a[] = {1, 2, 3};  
    auto begin = boost::make_transform_iterator(std::begin(a), d);  
    auto end = boost::make_transform_iterator(std::end(a), d);  
    static_assert(std::bidirectional_iterator<decltype(std::begin(a))>);  
    static_assert(std::bidirectional_iterator<decltype(begin)>); // this fails  
    for (auto i = begin; i != end; ++i) {  
        std::cout << *i << " ";  
    }  
}  
```  
  
adding something like `using iterator_category = std::iterator_traits<Iterator>::iterator_category;` around https://github.com/boostorg/iterator/blob/4b40364d6d110b130207bc498791e00050f94d6b/include/boost/iterator/transform_iterator.hpp#L93 seems to fix this for the case we ran into.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-10-30 10:41:57 UTC  
> Updated at: 2024-10-30 10:54:20 UTC  
> Url: https://github.com/boostorg/iterator/issues/88#issuecomment-2446525191  

> https://www.boost.org/doc/libs/1_85_0/libs/iterator/doc/transform_iterator.html says it typedefs `iterator_category` based on the underlying iterator. However, nothing in https://github.com/boostorg/iterator/blob/develop/include/boost/iterator/transform_iterator.hpp seems to indicate that it's doing so.  
  
The `iterator_category` type is provided by [`iterator_facade_base`](https://github.com/boostorg/iterator/blob/4b40364d6d110b130207bc498791e00050f94d6b/include/boost/iterator/iterator_facade.hpp#L732) from which `transform_iterator` ultimately derives (through `iterator_adaptor` and `iterator_facade`).  
  
The problem is that the `std::bidirectional_iterator` concept requires the iterator to dereference to an actual reference type and your `transform_iterator` dereferences to an rvalue. This is because the transform function `d` returns an rvalue. This is expected behavior. If you change `d` definition to e.g. `auto d = [](int& i) -> int& { return i; };` then the code [compiles](https://godbolt.org/z/hdbj4xdrs).

---

## Comment 2

> Username: aeubanks  
> Created at: 2024-10-30 16:20:21 UTC  
> Url: https://github.com/boostorg/iterator/issues/88#issuecomment-2447700680  

@Lastique ah I had misread the class hierarchy. However, someone had pointed out that https://en.cppreference.com/w/cpp/iterator/bidirectional_iterator#Notes says `Unlike the LegacyBidirectionalIterator requirements, the bidirectional_iterator concept does not require dereference to return an lvalue.`

---

## Comment 3

> Username: Lastique  
> Created at: 2024-10-30 16:55:23 UTC  
> Updated at: 2024-10-30 17:35:27 UTC  
> Url: https://github.com/boostorg/iterator/issues/88#issuecomment-2447804035  

<s>Yes, I've been inaccurate, but the point still stands. In C++20, the [`std::bidirectional_iterator`](http://eel.is/c++draft/iterator.concept.bidir) concept includes [`std::input_or_output_iterator`](http://eel.is/c++draft/iterator.concept.iterator) requirements, and that concept requires the result of dereferencing the iterator to be bindable to an lvalue reference (the [`can-reference`](http://eel.is/c++draft/iterator.synopsis#concept:can-reference) requirement). So, while it doesn't strictly require a reference type as a result of dereferencing the iterator, an `int` rvalue is still not allowed.</s>  
  
<s>Though I must say my understanding of concepts is rather limited, so I may be misinterpreting something.</s>

---

## Comment 4

> Username: Lastique  
> Created at: 2024-10-30 17:24:32 UTC  
> Url: https://github.com/boostorg/iterator/issues/88#issuecomment-2447875807  

Ah, scratch my last reply. The issue is actually that the `iterator_category` type defined by the iterator is not one of the standard iterator category tags but a synthesized category `iterator_category_with_traversal<std::input_iterator_tag, boost::iterators::random_access_traversal_tag>` (i.e an input iterator that supports random access traversal). Again, this is because the reference type of the iterator is not an actual reference, which was the requirement for forward iterators prior to C++20.
