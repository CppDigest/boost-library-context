# #80 - iterator_adaptor and std::prev [Closed]

> Username: sloriot  
> Created at: 2023-06-27 09:07:34 UTC  
> Updated at: 2023-06-27 11:49:20 UTC  
> Closed at: 2023-06-27 10:54:17 UTC  
> Url: https://github.com/boostorg/iterator/issues/80  

Hi,  
  
I have some troubles with `iterator_adaptor` with the default `CategoryOrTraversal` and `std::prev`. Indeed, in order to have it work I need to manually pass  `std::bidirectional_iterator_tag`. With g++ on linux I have an infinite loop with the default tag and MSVC gives the following error:  
  
```  
1>C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.35.32215\include\xutility(1241,19): error C2338: static_assert failed: 'prev requires bidirectional iterator'  
```  
  
Minimal code:  
```  
#include <list>  
#include <iostream>  
#include <boost/iterator/iterator_adaptor.hpp>  
  
// infinite loop or compilation error with the following  
#define Base boost::iterator_adaptor<Adapted, std::list<int>::iterator, int, boost::use_default, int>  
// no error with the following  
//#define Base boost::iterator_adaptor<Adapted, std::list<int>::iterator, int, std::bidirectional_iterator_tag, int>  
  
struct Adapted  
  : public Base  
{  
  Adapted() {}  
  
  Adapted(std::list<int>::iterator it)  
    : Base(it)  
  {}  
  
private:  
  friend class boost::iterator_core_access;  
  int dereference() const { return *this->base(); }  
};  
  
int main()  
{  
  
  std::list<int> l;  
  l.push_back(0);  
  l.push_back(1);  
  l.push_back(2);  
  
  Adapted ai(std::next(l.begin()));  
  
  std::cout << typeid(std::iterator_traits<Adapted>::iterator_category).name() << "\n";  
  
  std::cout << *ai << "\n";  
  std::cout << *std::next(ai) << "\n";  
  std::cout << *std::prev(ai) << "\n"; // infinite loop with g++ and compilation error with MSVC  
  
  return 0;  
}  
```

---

## Comment 1

> Username: afabri  
> Created at: 2023-06-27 09:09:56 UTC  
> Url: https://github.com/boostorg/iterator/issues/80#issuecomment-1609101392  

And the `typeid().name()`    is `struct boost::iterators::detail::iterator_category_with_traversal<struct std::input_iterator_tag,struct boost::iterators::bidirectional_traversal_tag>`

---

## Comment 2

> Username: Lastique  
> Created at: 2023-06-27 10:54:17 UTC  
> Url: https://github.com/boostorg/iterator/issues/80#issuecomment-1609265622  

This is because a conforming bidirectional iterator must have a reference type as its `reference_type`. In your case, you are explicitly specifying `int` as the reference type, which means the resulting iterator is an input iterator with the capability of bidirectional traversal, which is what `std::iterator_traits<Adapted>::iterator_category` is telling you. This kind of iterator is not fully supported by the standard library because, unlike Boost.Iterator, it does not separate the value category and traversal. It interprets this iterator as an input iterator (as the closest category that supports all operations on the iterator), and `std::prev` does not support input iterators. MSVC gives you a hard compile time error, and gcc/libstdc++ ends up invoking `std::advance` on it with a negative distance, which turns into an infinite loop. libstdc++ could definitely do better in this case, I suggest you report this to [gcc bugzilla](https://gcc.gnu.org/bugzilla/).  
  
When you override the iterator category with `std::bidirectional_iterator_tag`, you are producing a non-conforming bidirectional iterator. This allows you to trick the standard libraries, but may break elsewhere.  
  
The correct solution would be to use [`boost::next`/`boost::prior`](https://github.com/boostorg/iterator/blob/80bb1ac9e401d0d679718e29bef2f2aaf0123fcb/include/boost/next_prior.hpp) instead of `std::next`/`std::prev`.

---

## Comment 3

> Username: sloriot  
> Created at: 2023-06-27 11:49:20 UTC  
> Url: https://github.com/boostorg/iterator/issues/80#issuecomment-1609341391  

Thanks a lot for clarifying!
