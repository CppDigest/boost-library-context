# #3 - iterator_category vs iterator_concept [Closed]

> Username: akrzemi1  
> Created at: 2019-12-04 15:36:39 UTC  
> Updated at: 2019-12-09 16:22:50 UTC  
> Closed at: 2019-12-07 19:57:36 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/3  

I am not sure how I would implement a `zip_iterator` using `iterator_interface`. The problem with a zip iterator composed of two random-access iterators is that it has as its `iterator_traits<I>::reference` something that is not a reference:   
  
```c++  
std::tuple<typename Iter1::reference, typename Iter2::reference>  
```  
  
In C++17 and earlier it is required that for input iterators `iterator_traits<I>::reference` must be a reference, therefore such iterator has category `input_iterator_tag`. But in C++20 this requirement is lifted and we can label this iterator with `random_access_iterator_tag`. C++20 accommodates this "schism" by providing two distinct fields in `iterator_traits`, one for C++17 algorithms and one for "ranges" algorithms. So a zip iterator would be customized as:  
  
```c++  
template <typename Iter1, typename Iter2>  
struct iterator_traits<zip_iterator<Iter1, Iter2>>  
{  
  using iterator_category = std::input_iterator_tag; // for C++17 algos  
  using iterator_concept = std::random_access_iterator_tag; // for ranges algos  
  // ...  
};  
```  
  
Can this be supported in `iterator_interface`? That would require potentially providing two tags in the in the parameter list to template `iterator_interface<>`.

---

## Comment 1

> Username: tzlaine  
> Created at: 2019-12-07 19:57:36 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/3#issuecomment-562881364  

This is already supported -- it was a consideration fro the start,  Here is an example of where it appears in the tests:  
  
https://github.com/tzlaine/stl_interfaces/blob/master/test/random_access.cpp#L227  
  
It also appears in an example in the docs:  
  
https://tzlaine.github.io/stl_interfaces/doc/html/boost_stlinterfaces__proposed_/examples/zip_iterator___proxy_iterator.html  
  
Note that these are all conforming C++20 `std::random_access_iterator`s, and though they are technically not conforming Cpp17RandomAccessIterators (the pre-concepts definition), in practice they work with C++17-and-before algorithms.  
  
This applies to other iterator categories as well; random access was just an example.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2019-12-09 07:32:01 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/3#issuecomment-563103001  

The core of this issue is, what if I am not satisfied with the answer "maybe this is incorrect, but should work in practice" and I want to do it in full conformance of C++17 iterator constraints. Does the library allow me to do it? And if so, how would it look like?

---

## Comment 3

> Username: tzlaine  
> Created at: 2019-12-09 16:22:50 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/3#issuecomment-563316364  

No, the library can't help you here, because conforming to the Cpp17RaqndomAccessIterator requirements is not possible with a proxy iterator.  That is impossible.  However, none of the big-3 standard library implementations misbehaves if you do use a properly constructed zip-iterator in a call to std::sort.
