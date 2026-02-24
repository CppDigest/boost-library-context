# #135 - use of boost::reversed_iterator within boost::reversed_range adaptor [Open]

> Username: smitsyn  
> Created at: 2022-05-31 14:52:22 UTC  
> Updated at: 2022-05-31 15:24:05 UTC  
> Url: https://github.com/boostorg/range/issues/135  

We use custom `span` class template, and it works incorrectly with boost::adapters::reverse. Link to reproduce:  
  
https://godbolt.org/z/1Yxvqvj4r  
  
Executable compiled with clang prints incorrect value, with gcc - exits with nonzero code, and msvc compiler emits a warning. I believe godbolt doesn't support sanitizers ATM. Non-const span is provided and works fine.  
  
I believe the problem is that iterator_range::operator[] returns by reference and uses boost::reverse_iterator::operator[], but the latter reserved a right to return by value. According to docs ( https://www.boost.org/doc/libs/1_79_0/libs/iterator/doc/reverse_iterator.html ) it is "Random Access Iterator", and according to other docs ( https://www.boost.org/doc/libs/1_79_0/libs/iterator/doc/new-iter-concepts.html#design ), "operator[] is only required to return something convertible to the value_type (for a Readable Iterator)", which reverse_iterator does ( https://www.boost.org/doc/libs/1_79_0/libs/iterator/doc/new-iter-concepts.html#random-access-traversal-iterators-lib-random-access-traversal-iterators ): "`a[n]` has return type `convertible to T`" (for random access iterator as defined in now quite old "New Iterator Concepts").  
  
Note that some std::reverse_iterator implementations return by reference in `const T` case too and could work with `boost::iterator_range`.

---

## Comment 1

> Username: smitsyn  
> Created at: 2022-05-31 15:17:40 UTC  
> Url: https://github.com/boostorg/range/issues/135#issuecomment-1142272411  

When #122 is merged, this could fail with std::span too.
