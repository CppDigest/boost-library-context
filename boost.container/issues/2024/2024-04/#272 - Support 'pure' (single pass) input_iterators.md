# #272 - Support 'pure' (single pass) input_iterators [Closed]

> Username: psiha  
> Created at: 2024-04-10 10:44:04 UTC  
> Updated at: 2024-09-30 21:01:55 UTC  
> Closed at: 2024-09-30 21:01:55 UTC  
> Url: https://github.com/boostorg/container/issues/272  

Current assign() implementation from iterator pairs is broken for single-pass iterators (https://en.cppreference.com/w/cpp/iterator/input_iterator) since it assumes that the iterators satisfy at least the forward-iterator requirements (w/o checking those).

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-06-13 12:08:31 UTC  
> Updated at: 2024-06-13 12:13:42 UTC  
> Url: https://github.com/boostorg/container/issues/272#issuecomment-2165471797  

Boost.Container, at least sequence containers, should support "assign" from input iterators and that is tested in the library tests. Do you have an example that can reproduce the problem?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-08-18 22:12:32 UTC  
> Url: https://github.com/boostorg/container/issues/272#issuecomment-2295414143  

Closing this issue as assign from input iterator is already checked.

---

## Comment 3

> Username: psiha  
> Created at: 2024-08-19 07:27:13 UTC  
> Url: https://github.com/boostorg/container/issues/272#issuecomment-2295852085  

> Boost.Container, at least sequence containers, should support "assign" from input iterators and that is tested in the library tests. Do you have an example that can reproduce the problem?  
  
See here https://github.com/microsoft/STL/blob/b191409cec71aa837072a5d1ebafb041989c71dd/stl/inc/vector#L1245  
...you don't handle the final else 'uncounted range' case.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2024-08-19 20:30:23 UTC  
> Url: https://github.com/boostorg/container/issues/272#issuecomment-2297395936  

It's implemented using overloads and SFINAE. The function for input iterators is this:  
  
https://github.com/boostorg/container/blob/develop/include/boost/container/vector.hpp#L1320

---

## Comment 5

> Username: igaztanaga  
> Created at: 2024-09-30 21:01:55 UTC  
> Url: https://github.com/boostorg/container/issues/272#issuecomment-2384138551  

Closing this issue as input iterators are implemented and and tested (https://github.com/boostorg/container/blob/develop/test/vector_test.hpp#L491)
